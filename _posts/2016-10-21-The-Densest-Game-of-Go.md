---
layout: post
title: The Densest Game of Go
tags: Baduk Python
---

Just the other day, I had a very low scoring game of Go, and both sides had very few points. That got me thinking: "What's the lowest scoring game ever played?" This question is not well-defined because of captures and KOs. So I re-worded it to: "What's the game that had the most number of living stones at the end?" or the "densest game."

I did some research, and there are constructed games like [this one](http://senseis.xmp.net/?WholeBoardSeki), but no list of dense games. And of course, I wasn't planning on riffling through thousands of game records and counting the stones.

I found out that I could download Go4Go's professional [game database](http://www.go4go.net/go/games/delivery). So I spent a quiet evening to write a [scrappy little python script](https://gist.github.com/justecorruptio/cd5d135a77f70e8315d242b5f0c41d40) to parse through the 58k games and answer this for me.

The result is this amazing game from July 3rd, 2012 between Chen Yaoye and Zhang Dongyue. After 382 moves, there were 317 living stones at the end, or 87.8% of the board.

<sgf>
(;DT[2012-07-03]EV[4th Chinese Lanke Cup]RO[round 1]
PB[Chen Yaoye]BR[9p]PW[Zhang Dongyue]WR[5p]
KM[7.5]RE[B+3.5]SO[Go4Go.net]
;B[pd];W[dd];B[pq];W[dq];B[qk];W[mq];B[co];W[po];B[np];W[mp];B[no];W[nq];B[oq];W[mo];B[nn];W[pl];B[qn];W[pn];B[qo];W[ql];B[om];W[ip];B[pj];W[qp];B[pp];W[ol];B[nj];W[ml];B[mn];W[qf];B[qe];W[pf];B[nd];W[mf];B[ph];W[ng];B[rf];W[rg];B[re];W[rp];B[pm];W[qm];B[rn];W[sn];B[oo];W[rm];B[on];W[qi];B[pi];W[rk];B[qh];W[rh];B[rj];W[ri];B[qj];W[sj];B[of];W[og];B[nf];W[qg];B[pg];W[mg];B[mi];W[mk];B[ki];W[ld];B[mc];W[lc];B[ii];W[kk];B[jg];W[id];B[hk];W[fp];B[fc];W[gd];B[dc];W[cc];B[cd];W[de];B[cb];W[bc];B[eb];W[bb];B[fd];W[hg];B[gh];W[fe];B[ge];W[ff];B[hd];W[ie];B[km];W[jm];B[jn];W[jl];B[jp];W[jq];B[io];W[kp];B[ko];W[hl];B[gl];W[ik];B[hj];W[gm];B[hm];W[hn];B[kq];W[jo];B[il];W[im];B[jp];W[hl];B[lp];W[lh];B[li];W[fl];B[jj];W[so];B[ej];W[hh];B[gk];W[el];B[jk];W[kl];B[cj];W[cl];B[cg];W[ca];B[ic];W[he];B[gc];W[jc];B[df];W[ee];B[db];W[bf];B[ib];W[gi];B[fh];W[fg];B[bg];W[cf];B[hi];W[iq];B[jr];W[eh];B[fi];W[ir];B[cm];W[bl];B[bq];W[br];B[cq];W[cr];B[dp];W[ep];B[dm];W[dl];B[eo];W[bn];B[bm];W[am];B[bo];W[di];B[dj];W[en];B[fo];W[go];B[dr];W[eq];B[er];W[fr];B[gp];W[fq];B[ar];W[es];B[fn];W[em];B[fm];W[cn];B[do];W[dn];B[gn];W[ho];B[hm];W[ln];B[lm];W[gm];B[bs];W[ds];B[hm];W[oc];B[od];W[gm];B[dr];W[cs];B[hm];W[pc];B[gm];W[rc];B[mb];W[nb];B[la];W[in];B[na];W[oa];B[nc];W[ma];B[bj];W[cm];B[na];W[lq];B[ob];W[lo];B[kn];W[kp];B[gq];W[gr];B[lp];W[kr];B[kp];W[mr];B[mm];W[dg];B[rr];W[ok];B[kf];W[le];B[os];W[ei];B[fk];W[bh];B[ch];W[ag];B[ci];W[mh];B[ni];W[pe];B[qd];W[jb];B[ja];W[kb];B[ba];W[ka];B[bd];W[aa];B[da];W[ce];B[ba];W[ad];B[ab];W[be];B[ia];W[lb];B[ms];W[pb];B[pa];W[rb];B[ma];W[qa];B[qc];W[qb];B[oa];W[sf];B[sd];W[nr];B[or];W[lr];B[ke];W[kd];B[je];W[jd];B[ns];W[sr];B[rq];W[sq];B[il];W[kh];B[jh];W[hl];B[ls];W[js];B[il];W[oj];B[oi];W[hl];B[ks];W[jr];B[il];W[gg];B[fj];W[hl];B[if];W[hf];B[il];W[gb];B[ed];W[hl];B[eg];W[ef];B[il];W[qq];B[qr];W[hl];B[dh];W[eg];B[il];W[fb];B[ij];W[gf];B[hb];W[gd];B[hc];W[ao];B[se];W[si];B[sg];W[bp];B[cp];W[ap];B[aq];W[an];B[rs];W[ga];B[ge];W[sc];B[sa];W[gd];B[fa];W[ac];B[ak];W[aa];B[ca];W[ai];B[as];W[ab];B[ha];W[ge];B[jo];W[sp];B[ro];W[sm];B[bi];W[ah];B[aj];W[af];B[jf];W[lf];B[kg];W[ig];B[ih];W[kj];B[ji];W[lj];B[ll];W[lk];B[mj];W[nl];B[nk];W[nm];B[pk];W[ek];B[dk];W[ck];B[bk];W[al];B[oh];W[nh];B[oe];W[me];B[rd];W[sk];B[sf];W[sh];B[sb];W[ne];B[ra];W[md];B[ss];W[lg])
</sgf>

Honorable Mentions:

* 2011-06-07, Wang Lei vs Yoda Norimoto: 314
* 2012-01-07, Wu Guangya vs Park Junghwan: 312 
* 2011-03-15, Zhang Wei vs Mao Ruilong: 312
* 2006-06-13, Cho Hyeyeon vs Lee Hajin: 309
* 2010-01-27, Hu Yaoyu vs Wang Zheming: 308






