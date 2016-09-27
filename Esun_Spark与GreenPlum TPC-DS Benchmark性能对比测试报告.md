# Esun_Spark与GreenPlum TPC-DS Benchmark性能对比测试报告

标签（空格分隔）： Spark TPC 测试


Author： [@seven.loo][1] 
2016 年 09月 12日

---
## 背景              
基于TPC-DS基准测试工具，生成2TB数据分别导入到Spark和GreenPlum中，统一执行54个基准测试SQL脚本，通过参数优化、存储格式优化，对比测试Spark和GreenPlum的性能。
## 环境描述
![image_1atabhuiupqf1j4cr4rlqvgem.png-10.9kB][2]

## 测试数据
通过TPC-DS工具生成2T的数据，其中转成Parquert+压缩文件格式存储大小为651.1 G。
![表大小.jpg-108.6kB][3]
## 测试结果

 - **测试参数设置**
 主要设置spark优化后的参数、yarn资源分配等
![image_1asu5irj8r0lsb118en1eg0nnj2m.png-23.4kB][4]

其中：
  Spark数据的存储格式为：Parquert+压缩
  GP数据的存储格式为：Text+行存储

 - **总体测试结果对比**
 SQL优化前后的总体时间对比（All	Values	in	Seconds）
![image_1atat65m97t118tj160a10u26ct33.png-11.5kB][5]
上图结果表明：
  - 在未做SQL优化情况下，Spark的整体执行速度是GP的耗时的45%
  - 经过SQL优化后，GP整体执行速度提升了56%，而Spark提升了62%，Spark对比GP是GP耗时的35%

 - **GP测试结果**
![image_1asjnrrde16l11fan15ipqm2mp79.png-9.2kB][6]
GP存储格式改成列存储，整体的执行效率比行存储更慢。
 - **Spark各SQL执行明细：**
![测试结果.png-66.1kB][7]

 - **各SQL执行对比**
- ***query12***
   ![image_1ataqgun611ak1io51a211hm1doj29.png-11.2kB][8]
   - ***query13***
   ![image_1atat28bfetq1bprve81r141e3v2m.png-11.8kB][9]
   - ***query15***
   ![image_1atat88nufiv1n4jk60rpe1dj83g.png-12.1kB][10]
   - ***query17***
   ![image_1atat9ua6t1915kuki61rfh104u3t.png-12.1kB][11]
   - ***query18***
   ![image_1atatc6ahn001v3rvkn1t2nc534a.png-11.9kB][12]
   - ***query19***
   ![image_1atatumpkegp1s6fqaqqap1vd74n.png-11.6kB][13]
   - ***query20***
   ![image_1atau0cijae716r03lut3264o54.png-11.8kB][14]
   - ***query21***
   ![image_1atau5909n41ceg13koj59jep5h.png-11kB][15]
   - ***query22***
   ![image_1atav5t1g7lt9qefvo17edd0tef.png-11.2kB][16]
   - ***query25***
   ![image_1atav6jvdgg3huj186j1o3b8l5es.png-12kB][17]
   - ***query26***
   ![image_1atav36jg1lt71j4l9u81p1ebfe2.png-11.7kB][18]
   - ***query27***
   ![image_1atav2e7i1h3v1cvo188c1sootobdl.png-11.5kB][19]
   - ***query28***
   ![image_1atav23pu1ol11gd91sb4135clvd8.png-12.4kB][20]
   - ***query29***
   ![image_1atav1l9t1rnr1gj114uq17sjci4cr.png-12.5kB][21]
   - ***query32***
   ![image_1atav18rj1njnueft4j1c8q16tcce.png-11.6kB][22]
   - ***query34***
   ![image_1atav0mek1dvt1abq19rn1t1felic1.png-11.6kB][23]
   - ***query3***
   ![image_1atav063j1ou6m3j1cgj57d1v6jbk.png-11.5kB][24]
   - ***query40***
   ![image_1atauvqn11gcm13721k6b19j91dnsb7.png-11.8kB][25]
   - ***query42***
   ![image_1atauvcg81f1sm1nqup1hc31jfkaq.png-11.4kB][26]
   - ***query43***
   ![image_1atauuv0ncps1og11dnk14o51f3qad.png-11.8kB][27]
   - ***query46***
   ![image_1atav8bfc1he5h6aht3r7nrunf9.png-11.7kB][28]
   - ***query48***
   ![image_1atav8s5bdkepb6116m1cup1ge9fm.png-11.8kB][29]
   - ***query49***
   ![image_1atavbst01dl93ce1ma4fav1mo5g3.png-11.8kB][30]
   - ***query50***
   ![image_1atavchlbe3714h9mcf10m1ngngg.png-12.1kB][31]
   - ***query51***
   ![image_1atavdaur1lpc1km014101ptq1mvvgt.png-11.5kB][32]
   - ***query52***
   ![image_1atavee8o1vi5u8o1ni3ek814slha.png-11.4kB][33]
   - ***query54***
   ![image_1atavf0oo617cca2vh59f1trqhn.png-11.7kB][34]
   - ***query55***
   ![image_1atavgpbo30d1tcm1l1h78c1vc8i4.png-11.4kB][35]
   - ***query58***
   ![image_1atavha4u1atgh091o8p1pv21srmih.png-11.8kB][36]
   - ***query66***
   ![image_1atavi5mam0vrhpk99uiu1995iu.png-11.7kB][37]
   - ***query67***
   ![image_1atavim99fi11iug51jacpl7jb.png-12.3kB][38]
   - ***query68***
   ![image_1atavj1ri4fgngf12b019se1u6ejo.png-11.6kB][39]
   - ***query71***
   ![image_1atavjlr6147u53s1n9b5ka1a14k5.png-11.5kB][40]
   - ***query73***
   ![image_1atavkffe1n3411rnghigm011p4ki.png-11.7kB][41]
   - ***query75***
   ![image_1atavkqr6b1vvob19761oe616mukv.png-12.4kB][42]
   - ***query76***
   ![image_1atavldj5f5j121h1uu0635nl9lc.png-12kB][43]
   - ***query79***
   ![image_1atavlom91jqck9get81a457ftlp.png-11.6kB][44]
   - ***query7***
   ![image_1atavm5kjg5j3g4bs51j80qbvm6.png-11.5kB][45]
   - ***query80***
   ![image_1atavmnbipmd17j81or51hb7109vmj.png-12kB][46]
   - ***query82***
   ![image_1atavn4o7ir81rcq13qn1q9d1r9in0.png-11.8kB][47]
   - ***query84***
   ![image_1atavnoed18h18omh0eogp3rmnd.png-12.3kB][48]
   - ***query85***
   ![image_1atavo8l71n2ifge8f17s41qbfnq.png-11.8kB][49]
   - ***query87***
   ![image_1atavoppc1ugniebv0813tt14qko7.png-12.1kB][50]
   - ***query88***
   ![image_1atavp61a82u84q9okisf1okvok.png-11.4kB][51]
   - ***query89***
   ![image_1atavppb2n291c6i1hgc1m1qgemp1.png-11.6kB][52]
   - ***query90***
   ![image_1atavq7ntvd8qcom2f1rrc1hp0pe.png-11.7kB][53]
   - ***query91***
   ![image_1atavqnvokpp1meq157v1us0103bpr.png-11.4kB][54]
   - ***query92***
   ![image_1atavr4q2tvo12251kok15k31e2rq8.png-12kB][55]
   - ***query93***
   ![image_1atavrj311v321fqjb8pqr01dgmql.png-12.4kB][56]
   - ***query94***
   ![image_1atavrv4e1uep1v4b4741nriuger2.png-12kB][57]
   - ***query95***
   ![image_1atavseqgc321ai457lbgu2l8rf.png-11.8kB][58]
   - ***query96***
   ![image_1atavtfnf1ccg1plu147b1hjlr13rs.png-12kB][59]
   - ***query97***
   ![image_1atavtte71fv31r4219oj10t91tkks9.png-12.3kB][60]
   - ***query98***
   ![image_1atavugaref18ihatr682ln4sm.png-11.2kB][61]
##  优化总结
- **Spark优化**
   - ***executor、core、memory***
   在使用完全部资源的前提下，使executor数量尽量最小，减少shuffle的数据量。
   在shuffle数据量大的情况下，如果GC时间太长甚至出现oom，对于StaticMemoryManager静态内存管理，可适度加大executor内存，调整spark.shuffle.memoryFraction=0.6，spark.storage.memoryFraction=0.2，适度提高spark.yarn.executor.memoryOverhead的值，均能提高spark性能。
   - ***广播变量***
   当有小表时，尽量广播小表，使用BroadcastHashJoin来代替Common Join（SortMergeJoin和HashJoin）。可以设置**spark.sql.autoBroadcastJoinThreshold**大小，来调整需要广播的表。注意，广播太大的表可能比Common Join耗时长。
   - ***分区***
   参数**spark.shuffle.partition**对Spark性能影响大。这个参数决定在做shuffle Read的时候，启动的Task数量。 在一定的数据量下，如果task数量太多，每个task处理的数据量少，造成时间的浪费；如果task数量太少，每个task处理的数据量变大，可能导致频繁GC甚至内存溢出。每个SQL，根据其业务的不同，Executor内存大小不同，其合适的partition数不同。 做法是由大到小变动partition个数，找到一个比最快稍大一点的值。
   - ***SQL优化***
     - 充分利用spark并行执行的优点，把串联执行的多表join根据关联条件拆分成可并行
     - 使聚合或者过滤条件提前，能有效减少数据量用于join计算，能有效提高SQL执行效率
     - 利用分区条件，减小搜索范围,减小数据量
     - distinct语句改为 group by语句
     - 预先cache table，对于重复使用的表有很好的性能提升
    


  [1]: http://static.zybuluo.com/sevenloo/fwmtbmq28iyzmsx10zqv0ljh/%E8%BD%AF%E7%A1%AC%E4%BB%B6.jpg
  [2]: http://static.zybuluo.com/sevenloo/w5ytm5t017l7kaluoedx2s03/image_1atabhuiupqf1j4cr4rlqvgem.png
  [3]: http://static.zybuluo.com/sevenloo/ylof3c4y7bi9e354vt3hatp3/%E8%A1%A8%E5%A4%A7%E5%B0%8F.jpg
  [4]: http://static.zybuluo.com/sevenloo/w5syazudowzb9b1o9ehrmlqf/image_1asu5irj8r0lsb118en1eg0nnj2m.png
  [5]: http://static.zybuluo.com/sevenloo/5x1n27f3qi3d4g0c05zsykxk/image_1atat65m97t118tj160a10u26ct33.png
  [6]: http://static.zybuluo.com/sevenloo/utues72movjrxgow9coe2vnc/image_1asjnrrde16l11fan15ipqm2mp79.png
  [7]: http://static.zybuluo.com/sevenloo/e7y6bb4vcmgzie4nk0g7dnff/%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png
  [8]: http://static.zybuluo.com/sevenloo/szqzk3zisnxz0ltldbi58k0q/image_1ataqgun611ak1io51a211hm1doj29.png
  [9]: http://static.zybuluo.com/sevenloo/n0ii21wclzt3mjg5ryxz06ug/image_1atat28bfetq1bprve81r141e3v2m.png
  [10]: http://static.zybuluo.com/sevenloo/rz2efph08k6wwbc9u9zuw6si/image_1atat88nufiv1n4jk60rpe1dj83g.png
  [11]: http://static.zybuluo.com/sevenloo/j1km7vfzb25lkfovmjif6zr2/image_1atat9ua6t1915kuki61rfh104u3t.png
  [12]: http://static.zybuluo.com/sevenloo/7k7a069rz1uzy0zwexv0q8x7/image_1atatc6ahn001v3rvkn1t2nc534a.png
  [13]: http://static.zybuluo.com/sevenloo/149tkbuilh0y8athabfk8rn8/image_1atatumpkegp1s6fqaqqap1vd74n.png
  [14]: http://static.zybuluo.com/sevenloo/d0y9oxq1w6c2seviw2ue73r6/image_1atau0cijae716r03lut3264o54.png
  [15]: http://static.zybuluo.com/sevenloo/kffplbwbeotuhk543xyowbew/image_1atau5909n41ceg13koj59jep5h.png
  [16]: http://static.zybuluo.com/sevenloo/uahm9scm4lnl7xl8wkuo42tb/image_1atav5t1g7lt9qefvo17edd0tef.png
  [17]: http://static.zybuluo.com/sevenloo/r518wcv3d6tzlhr7zkl0lpyp/image_1atav6jvdgg3huj186j1o3b8l5es.png
  [18]: http://static.zybuluo.com/sevenloo/t0ijhhnf1uk6kaudawa7hpog/image_1atav36jg1lt71j4l9u81p1ebfe2.png
  [19]: http://static.zybuluo.com/sevenloo/sq6yo8fazyv52bc4nxcougpd/image_1atav2e7i1h3v1cvo188c1sootobdl.png
  [20]: http://static.zybuluo.com/sevenloo/9uwbnqhj3ojpy303v07hiyir/image_1atav23pu1ol11gd91sb4135clvd8.png
  [21]: http://static.zybuluo.com/sevenloo/k1pz2gscv9cw9u5jy8q5qva3/image_1atav1l9t1rnr1gj114uq17sjci4cr.png
  [22]: http://static.zybuluo.com/sevenloo/sqdeum0glzyvpw8gx394ur6s/image_1atav18rj1njnueft4j1c8q16tcce.png
  [23]: http://static.zybuluo.com/sevenloo/3bdahh9lpnc9l3kmw9vmphgm/image_1atav0mek1dvt1abq19rn1t1felic1.png
  [24]: http://static.zybuluo.com/sevenloo/d2n3hyj1hlysfyp6vesbvk2k/image_1atav063j1ou6m3j1cgj57d1v6jbk.png
  [25]: http://static.zybuluo.com/sevenloo/ak172s9ro2vwh3typ9s048p6/image_1atauvqn11gcm13721k6b19j91dnsb7.png
  [26]: http://static.zybuluo.com/sevenloo/l8jpt8u15dil2qh2pyxt0t2q/image_1atauvcg81f1sm1nqup1hc31jfkaq.png
  [27]: http://static.zybuluo.com/sevenloo/pajtzzxs5j4u0w9lr11wyz0r/image_1atauuv0ncps1og11dnk14o51f3qad.png
  [28]: http://static.zybuluo.com/sevenloo/qdezt162cjce3ua0nrgwsypa/image_1atav8bfc1he5h6aht3r7nrunf9.png
  [29]: http://static.zybuluo.com/sevenloo/gyxwd5fsfgmk2utt9alzsfof/image_1atav8s5bdkepb6116m1cup1ge9fm.png
  [30]: http://static.zybuluo.com/sevenloo/evqucrwgf60p4hclcew2hxs8/image_1atavbst01dl93ce1ma4fav1mo5g3.png
  [31]: http://static.zybuluo.com/sevenloo/2se36w4mndeplxj08q1cxrjm/image_1atavchlbe3714h9mcf10m1ngngg.png
  [32]: http://static.zybuluo.com/sevenloo/0le3ka26kaxioj3q44zh84x0/image_1atavdaur1lpc1km014101ptq1mvvgt.png
  [33]: http://static.zybuluo.com/sevenloo/9n880vo54t2blvlcdwl8x5ai/image_1atavee8o1vi5u8o1ni3ek814slha.png
  [34]: http://static.zybuluo.com/sevenloo/0swdioteym0tf68d9unzmdb9/image_1atavf0oo617cca2vh59f1trqhn.png
  [35]: http://static.zybuluo.com/sevenloo/ef7kd7zimixeqth9ialphmws/image_1atavgpbo30d1tcm1l1h78c1vc8i4.png
  [36]: http://static.zybuluo.com/sevenloo/6sjkc42npji2gb2dpph8ntuy/image_1atavha4u1atgh091o8p1pv21srmih.png
  [37]: http://static.zybuluo.com/sevenloo/802u35tig6775if3zjv3nyme/image_1atavi5mam0vrhpk99uiu1995iu.png
  [38]: http://static.zybuluo.com/sevenloo/3qu7nhrh39xhbvtiferyn87y/image_1atavim99fi11iug51jacpl7jb.png
  [39]: http://static.zybuluo.com/sevenloo/hu5luov6k5goaqphedbh4ubs/image_1atavj1ri4fgngf12b019se1u6ejo.png
  [40]: http://static.zybuluo.com/sevenloo/9kc366o9k77uappozvctkqzi/image_1atavjlr6147u53s1n9b5ka1a14k5.png
  [41]: http://static.zybuluo.com/sevenloo/cac5gma90lh1hit2xuplbmmq/image_1atavkffe1n3411rnghigm011p4ki.png
  [42]: http://static.zybuluo.com/sevenloo/uio9xtwk2np0gk7pcjs3h0su/image_1atavkqr6b1vvob19761oe616mukv.png
  [43]: http://static.zybuluo.com/sevenloo/sflu1ud16pt22b2u3a3mi0sc/image_1atavldj5f5j121h1uu0635nl9lc.png
  [44]: http://static.zybuluo.com/sevenloo/mw9n8sb9l1f3zeyjbuslmxkc/image_1atavlom91jqck9get81a457ftlp.png
  [45]: http://static.zybuluo.com/sevenloo/110k49hsq3n2ttrp94bmj5v4/image_1atavm5kjg5j3g4bs51j80qbvm6.png
  [46]: http://static.zybuluo.com/sevenloo/u9b8qarl3wjrkkvpubzhm4dq/image_1atavmnbipmd17j81or51hb7109vmj.png
  [47]: http://static.zybuluo.com/sevenloo/rzjn1d6rwm7m8sip10czt86q/image_1atavn4o7ir81rcq13qn1q9d1r9in0.png
  [48]: http://static.zybuluo.com/sevenloo/bbne125van1floi17h7f606s/image_1atavnoed18h18omh0eogp3rmnd.png
  [49]: http://static.zybuluo.com/sevenloo/pc6uh302jpy5j50sj1d2qwz3/image_1atavo8l71n2ifge8f17s41qbfnq.png
  [50]: http://static.zybuluo.com/sevenloo/mw716ft4v6mb6cfxys31ontp/image_1atavoppc1ugniebv0813tt14qko7.png
  [51]: http://static.zybuluo.com/sevenloo/2jwtocdqtwvq5by2plhmipyo/image_1atavp61a82u84q9okisf1okvok.png
  [52]: http://static.zybuluo.com/sevenloo/f8it0nsz29cmkhn91n5pet3c/image_1atavppb2n291c6i1hgc1m1qgemp1.png
  [53]: http://static.zybuluo.com/sevenloo/d9upd0pej6alvn95p4dicq3g/image_1atavq7ntvd8qcom2f1rrc1hp0pe.png
  [54]: http://static.zybuluo.com/sevenloo/0vd97ll1asm2br3dt3ez2bmh/image_1atavqnvokpp1meq157v1us0103bpr.png
  [55]: http://static.zybuluo.com/sevenloo/vjp8gaav98i9eh2a88kiulyp/image_1atavr4q2tvo12251kok15k31e2rq8.png
  [56]: http://static.zybuluo.com/sevenloo/t1gnb20xyo79ivrdwk4b89ae/image_1atavrj311v321fqjb8pqr01dgmql.png
  [57]: http://static.zybuluo.com/sevenloo/grt7396a7p7af1wmvk571dcz/image_1atavrv4e1uep1v4b4741nriuger2.png
  [58]: http://static.zybuluo.com/sevenloo/rn4hnbgtbhz1syna81lm5j8x/image_1atavseqgc321ai457lbgu2l8rf.png
  [59]: http://static.zybuluo.com/sevenloo/xy2xda5ttjf197n2iuaynzw9/image_1atavtfnf1ccg1plu147b1hjlr13rs.png
  [60]: http://static.zybuluo.com/sevenloo/zh778jk3y20xxyfphfarbre6/image_1atavtte71fv31r4219oj10t91tkks9.png
  [61]: http://static.zybuluo.com/sevenloo/yjk77e9oo5ng9jncuk3q4jj1/image_1atavugaref18ihatr682ln4sm.png