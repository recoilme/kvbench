# KVBench

cloned from [tidwall/kvbench](https://github.com/tidwall/kvbench)

KVBench is a Redis server clone backed by a few different Go databases. 

It's intended to be used with the `redis-benchmark` command to test the performance of various Go databases.
It has support for redis pipelining.

this cloned version adds more kv databases and automatic scripts.

Features:

- Databases
  - [badger](https://github.com/dgraph-io/badger)
  - [BboltDB](https://github.com/etcd-io/bbolt)
  - [BoltDB](https://github.com/boltdb/bolt)
  - [buntdb](https://github.com/tidwall/buntdb)
  - [LevelDB](https://github.com/syndtr/goleveldb)
  - [cznic/kv](https://github.com/cznic/kv)
  - [rocksdb](https://github.com/tecbot/gorocksdb)
  - [pebble](https://github.com/petermattis/pebble)
  - [pogreb](https://github.com/akrylysov/pogreb)
  - [nutsdb](https://github.com/xujiajun/nutsdb)
  - [sniper](https://github.com/recoilme/sniper)
  - map (in-memory) with [AOF persistence](https://redis.io/topics/persistence)
  - btree (in-memory) with [AOF persistence](https://redis.io/topics/persistence)
- Option to disable fsync
- Compatible with Redis clients


## HDD benchmark

### nofsync

**throughputs**

|          | badger | bbolt  | bolt   | leveldb | kv    | buntdb  | pebble  | rocksdb | btree   | btree/memory | map     | map/memory |
| -------- | ------ | ------ | ------ | ------- | ----- | ------- | ------- | ------- | ------- | ------------ | ------- | ---------- |
| set      | 97826  | 12177  | 12053  | 157460  | 4484  | 33597   | 235619  | 240147  | 178144  | 466905       | 244180  | 851762     |
| setmixed | 1882   | 9863   | 7359   | 16244   | 229   | 1214    | 82808   | 83434   | 15660   | 18139        | 14586   | 15382      |
| get      | 406820 | 431317 | 446030 | 377650  | 19579 | 3906593 | 2183403 | 2096807 | 5386219 | 4712750      | 7254585 | 6564505    |
| getmixed | 419278 | 77152  | 201151 | 330812  | 9198  | 66713   | 1841265 | 1824536 | 1164785 | 1353110      | 2132537 | 2208846    |
| del      | 117681 | 5123   | 4386   | 144282  | 2777  | 107786  | 697507  | 681730  | 541060  | 715948       | 799636  | 1804057    |

**time (latency)**

|          | badger | bbolt  | bolt   | leveldb | kv      | buntdb | pebble | rocksdb | btree | btree/memory | map   | map/memory |
| -------- | ------ | ------ | ------ | ------- | ------- | ------ | ------ | ------- | ----- | ------------ | ----- | ---------- |
| set      | 255    | 2052   | 2074   | 158     | 5575    | 744    | 106    | 104     | 140   | 53           | 102   | 29         |
| setmixed | 531148 | 101380 | 135879 | 61560   | 4348616 | 823683 | 12076  | 11985   | 63853 | 55128        | 68556 | 65008      |
| get      | 61     | 57     | 56     | 66      | 1276    | 6      | 11     | 11      | 4     | 5            | 3     | 3          |
| getmixed | 59     | 324    | 124    | 75      | 2717    | 374    | 13     | 13      | 21    | 18           | 11    | 11         |
| del      | 212    | 4879   | 5699   | 173     | 9002    | 231    | 35     | 36      | 46    | 34           | 31    | 13         |

### fsync

**throughputs**

|          | badger | bbolt  | bolt   | leveldb | kv    | buntdb  | pebble  | rocksdb | btree   | btree/memory | map     | map/memory |
| -------- | ------ | ------ | ------ | ------- | ----- | ------- | ------- | ------- | ------- | ------------ | ------- | ---------- |
| set      | 647    | 43     | 41     | 1640    | 4452  | 66      | 1340    | 1411    | 68      | 533205       | 67      | 906938     |
| setmixed | 48     | 45     | 51     | 49      | 223   | 65      | 64      | 64      | 67      | 18863        | 67      | 16869      |
| get      | 534795 | 448613 | 442213 | 442645  | 19503 | 2906408 | 4024975 | 4139906 | 5477789 | 5101780      | 7054785 | 7195796    |
| getmixed | 9347   | 457678 | 452649 | 453488  | 8921  | 5248    | 3978090 | 4019577 | 3065    | 1452649      | 4831    | 1669711    |
| del      | 673    | 45     | 50     | 1007    | 2760  | 63      | 1421    | 1416    | 67      | 663769       | 67      | 1146117    |


**time (latency)**

|          | badger   | bbolt    | bolt     | leveldb  | kv      | buntdb   | pebble   | rocksdb  | btree    | btree/memory | map      | map/memory |
| -------- | -------- | -------- | -------- | -------- | ------- | -------- | -------- | -------- | -------- | ------------ | -------- | ---------- |
| set      | 38639    | 570124   | 601656   | 15239    | 5615    | 378254   | 18656    | 17707    | 367040   | 46           | 370182   | 27         |
| setmixed | 20720699 | 22148437 | 19480576 | 20366663 | 4483604 | 15187596 | 15552309 | 15576427 | 14889368 | 53011        | 14797227 | 59278      |
| get      | 46       | 55       | 56       | 56       | 1281    | 8        | 6        | 6        | 4        | 4            | 3        | 3          |
| getmixed | 2674     | 54       | 55       | 55       | 2802    | 4763     | 6        | 6        | 8155     | 17           | 5174     | 14         |
| del      | 37115    | 544103   | 495720   | 24816    | 9055    | 391819   | 17585    | 17645    | 369899   | 37           | 368537   | 21         |

## SSD benchmark

### nofsync

**throughputs**

|          | badger | bbolt  | bolt   | leveldb | kv    | buntdb  | pebble  | rocksdb | btree   | btree/memory | map     | map/memory |
| -------- | ------ | ------ | ------ | ------- | ----- | ------- | ------- | ------- | ------- | ------------ | ------- | ---------- |
| set      | 90704  | 12079  | 12515  | 136651  | 4738  | 39266   | 414214  | 418490  | 184419  | 446235       | 194885  | 652554     |
| setmixed | 4438   | 8250   | 9322   | 15855   | 700   | 4239    | 159341  | 162827  | 28274   | 28571        | 37373   | 41030      |
| get      | 415335 | 434284 | 432883 | 470476  | 13005 | 3184811 | 1846936 | 1948166 | 3844164 | 3259562      | 4571272 | 3866944    |
| getmixed | 375022 | 61022  | 228578 | 283312  | 14003 | 92759   | 1348567 | 1383842 | 653630  | 772654       | 1409143 | 1590783    |
| del      | 97531  | 4812   | 4866   | 99121   | 3166  | 126472  | 570835  | 582196  | 406421  | 717582       | 766169  | 1639898    |

**time (latency)**

|          | badger | bbolt  | bolt   | leveldb | kv      | buntdb | pebble | rocksdb | btree | btree/memory | map   | map/memory |
| -------- | ------ | ------ | ------ | ------- | ------- | ------ | ------ | ------- | ----- | ------------ | ----- | ---------- |
| set      | 551    | 4139   | 3995   | 365     | 10551   | 1273   | 120    | 119     | 271   | 112          | 256   | 76         |
| setmixed | 225325 | 121200 | 107265 | 63071   | 1428156 | 235884 | 6275   | 6141    | 35367 | 34999        | 26757 | 24372      |
| get      | 120    | 115    | 115    | 106     | 3844    | 15     | 27     | 25      | 13    | 15           | 10    | 12         |
| getmixed | 133    | 819    | 218    | 176     | 3570    | 539    | 37     | 36      | 76    | 64           | 35    | 31         |
| del      | 512    | 10389  | 10274  | 504     | 15790   | 395    | 87     | 85      | 123   | 69           | 65    | 30         |

### fsync

**throughputs**

|          | badger | bbolt  | bolt   | leveldb | kv    | buntdb  | pebble  | rocksdb | btree   | btree/memory | map     | map/memory |
| -------- | ------ | ------ | ------ | ------- | ----- | ------- | ------- | ------- | ------- | ------------ | ------- | ---------- |
| set      | 38099  | 4404   | 4145   | 59062   | 4666  | 6380    | 58455   | 58989   | 7675    | 457271       | 7556    | 762687     |
| get      | 402123 | 430888 | 436397 | 538062  | 6503  | 3238379 | 2011075 | 1949567 | 4438225 | 3401642      | 4852308 | 3981281    |
| getmixed | 128512 | 224395 | 386122 | 515329  | 11874 | 98078   | 1744643 | 1505685 | 133832  | 827284       | 142795  | 1709595    |
| del      | 42907  | 2846   | 3053   | 112733  | 2941  | 41396   | 63645   | 63378   | 57735   | 733436       | 20489   | 1759382    |
| setmixed | 5758   | 4746   | 5494   | 7365    | 593   | 4293    | 5329    | 5570    | 6659    | 30149        | 6896    | 42065      |


**time (latency)**

|          | badger | bbolt  | bolt   | leveldb | kv      | buntdb | pebble | rocksdb | btree  | btree/memory | map    | map/memory |
| -------- | ------ | ------ | ------ | ------- | ------- | ------ | ------ | ------- | ------ | ------------ | ------ | ---------- |
| set      | 1312   | 11352  | 12061  | 846     | 10715   | 7836   | 855    | 847     | 6514   | 109          | 6616   | 65         |
| setmixed | 173657 | 210685 | 182009 | 135776  | 1684290 | 232926 | 187630 | 179506  | 150154 | 33167        | 144992 | 23772      |
| get      | 124    | 116    | 114    | 92      | 7687    | 15     | 24     | 25      | 11     | 14           | 10     | 12         |
| getmixed | 389    | 222    | 129    | 97      | 4210    | 509    | 28     | 33      | 373    | 60           | 350    | 29         |
| del      | 1165   | 17565  | 16375  | 443     | 16995   | 1207   | 785    | 788     | 866    | 68           | 2440   | 28         |
