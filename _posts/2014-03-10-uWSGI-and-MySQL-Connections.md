---
layout: post
title: uWSGI and MySQL Connections
tags: Python MySQL uWSGI
---

The other day, I was investigating a minor performance issue with an internal API serivce.  This service is implemented with python running via uWSGI whose main purpose is: fetch data from MySQL, process it, and then return result to caller.  For some reason, exactly 1% of all calls to this service intermittently took 20-50ms longer than the rest.

The first lead in investigating this was that I knew a part of our uWSGI configuration looks something like this:

```ini
[uwsgi]
max-requests = 100
processes = 32
```
    
This means that each forked worker would serve 100 requests and automatically get respawned.  So the 1% and sporadic nature of this issue could be explained if something is occurred during respawn.

Next, as with any performance issue, we fire up a profiler in a development environment.  I also reduce the number of requests and max-processes to something easier to debug:

```ini
[uwsgi]
max-requests = 2
processes = 1
```
    
Then I inject the following snippet to profile the critical path in our service call and log the result.

import cProfile, pstats, logging, cStringIO
prof = cProfile.profile()

```python
# main_func is our function to profile
results = prof.runcall(main_func, *args)
prof.create_stats()
stream = CStringIO.StringIO()
stats = pstats.Stats(prof, stream=stream)
stats.print_stats()
logger = logging.getLogger()
logger.debug(stream.getvalue())
```
    
As expected every other API service call took slightly longer to run.  Upon inspecting the profile stats, I see that every other call is calling `_mysql.connect()`.  Aha!  What was happening was that that our fork-abusing uWSGI was killing our entire MySQL connection pool during every respawn.

So the fix becomes simple, make a connection to MySQL after worker `fork()` time, and it will be ready when a request comes in.  Luckily, uWSGI provides a convenient `@postfork` decorator.  So all we had to do is to add this to our application:

```python
application = OurApplication()

try:
    import uwsgi
    import uwsgidecorators
    
    @uwsgidecorators.postfork
    def prefork_db_connect():
    application.connect_db()

except ImportError, e:
    # we're not running in uWSGI environment
    pass
```