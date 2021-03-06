#ByteArrayExtensions

Hardware-accelerated Vectorized Memory Copy faster than the .NET Framework's `Buffer.BlockCopy` and `Array.Copy` for copies under 1024 bytes. 

CoreClr Issue: "Array.Copy & Buffer.BlockCopy x2 to x3 slower < 1kB" https://github.com/dotnet/coreclr/issues/2430

##Performance

[![Performance Graph](http://aoa.blob.core.windows.net/aspnet/VectorizedCopy888.png)](http://aoa.blob.core.windows.net/aspnet/VectorizedCopy.png)

---
##Usage

Given
```csharp
var src = byte[1024];
var dst = byte[1024];
var srcOffset = 64;
var dstOffset = 32;
var count = 64;
```
Rather than
```csharp
Array.Copy(src, srcOffset, dst, dstOffset, count);
```
Or
```csharp
Buffer.BlockCopy(src, srcOffset, dst, dstOffset, count);
```
Use
```csharp
src.VectorizedCopy(srcOffset, dst, dstOffset, count);
```

---
##Benchmarking Results

8.5 hours to run
```ini
BenchmarkDotNet-Dev=v0.8.0.0+
OS=Microsoft Windows NT 6.2.9200.0
Processor=Intel(R) Core(TM) i7-4720HQ CPU @ 2.60GHz, ProcessorCount=8
HostCLR=MS.NET 4.0.30319.42000, Arch=64-bit  [RyuJIT]
Type=MemCopyBench  Mode=Throughput  Platform=X64  Jit=RyuJit  .NET=HostFramework  toolchain=Classic  Runtime=Clr  Warmup=5  Target=10
```
Method         | Len  | AvrTime    | StdDev    | op/s           | % Delta vs ArrayCopy |
-------------- |-----:|-----------:|----------:|---------------:|---------------------:|
ArrayCopy      |    0 |  4.3432 ns | 0.0825 ns | 230,324,244.88 | 0.0% |
BlockCopy      |    0 |  4.8666 ns | 0.0886 ns | 205,545,857.61 | -10.8% |
VectorizedCopy |    0 |  2.0270 ns | 0.0346 ns | 493,472,059.76 | 114.3% |
ArrayCopy      |    1 |  5.7284 ns | 0.0951 ns | 174,613,397.66 | 0.0% |
BlockCopy      |    1 |  6.0099 ns | 0.0102 ns | 166,393,078.33 | -4.7% |
VectorizedCopy |    1 |  3.5840 ns | 0.0616 ns | 279,095,553.59 | 59.8% |
ArrayCopy      |    2 |  5.8000 ns | 0.0925 ns | 172,457,750.22 | 0.0% |
BlockCopy      |    2 |  5.8619 ns | 0.1000 ns | 170,641,279.28 | -1.1% |
VectorizedCopy |    2 |  3.8173 ns | 0.0027 ns | 261,962,919.83 | 51.9% |
ArrayCopy      |    3 |  5.7231 ns | 0.1000 ns | 174,780,635.43 | 0.0% |
BlockCopy      |    3 |  5.8703 ns | 0.1008 ns | 170,396,161.19 | -2.5% |
VectorizedCopy |    3 |  4.0668 ns | 0.0739 ns | 245,973,535.19 | 40.7% |
ArrayCopy      |    4 |  5.8710 ns | 0.0208 ns | 170,332,048.11 | 0.0% |
BlockCopy      |    4 |  5.9355 ns | 0.1026 ns | 168,528,148.03 | -1.1% |
VectorizedCopy |    4 |  3.5801 ns | 0.0603 ns | 279,400,369.43 | 64.0% |
ArrayCopy      |    5 |  5.8673 ns | 0.0104 ns | 170,437,225.19 | 0.0% |
BlockCopy      |    5 |  5.9407 ns | 0.1060 ns | 168,382,732.88 | -1.2% |
VectorizedCopy |    5 |  3.6774 ns | 0.0127 ns | 271,932,810.61 | 59.6% |
ArrayCopy      |    6 |  5.9410 ns | 0.0978 ns | 168,365,533.85 | 0.0% |
BlockCopy      |    6 |  6.1561 ns | 0.0152 ns | 162,442,647.10 | -3.5% |
VectorizedCopy |    6 |  4.1069 ns | 0.0139 ns | 243,496,948.01 | 44.6% |
ArrayCopy      |    7 |  5.8682 ns | 0.0978 ns | 170,455,514.03 | 0.0% |
BlockCopy      |    7 |  6.0837 ns | 0.1084 ns | 164,425,534.76 | -3.5% |
VectorizedCopy |    7 |  4.3471 ns | 0.0761 ns | 230,106,855.01 | 35.0% |
ArrayCopy      |    8 |  5.7960 ns | 0.1014 ns | 172,584,514.72 | 0.0% |
BlockCopy      |    8 |  5.9406 ns | 0.0969 ns | 168,375,832.85 | -2.4% |
VectorizedCopy |    8 |  3.6227 ns | 0.0597 ns | 276,111,777.08 | 60.0% |
ArrayCopy      |    9 |  5.8628 ns | 0.0100 ns | 170,568,210.23 | 0.0% |
BlockCopy      |    9 |  6.0097 ns | 0.0147 ns | 166,397,469.33 | -2.4% |
VectorizedCopy |    9 |  3.9651 ns | 0.0076 ns | 252,203,935.83 | 47.9% |
ArrayCopy      |   10 |  5.8639 ns | 0.0113 ns | 170,536,867.40 | 0.0% |
BlockCopy      |   10 |  5.9407 ns | 0.0975 ns | 168,374,467.10 | -1.3% |
VectorizedCopy |   10 |  4.2031 ns | 0.0708 ns | 237,984,581.70 | 39.6% |
ArrayCopy      |   11 |  6.0222 ns | 0.0384 ns | 166,059,674.28 | 0.0% |
BlockCopy      |   11 |  6.0856 ns | 0.1006 ns | 164,365,421.87 | -1.0% |
VectorizedCopy |   11 |  4.3980 ns | 0.0114 ns | 227,378,140.50 | 36.9% |
ArrayCopy      |   12 |  5.7978 ns | 0.0968 ns | 172,525,275.05 | 0.0% |
BlockCopy      |   12 |  5.9368 ns | 0.1118 ns | 168,499,595.33 | -2.3% |
VectorizedCopy |   12 |  3.8656 ns | 0.0701 ns | 258,771,249.76 | 50.0% |
ArrayCopy      |   13 |  5.8599 ns | 0.0985 ns | 170,697,280.28 | 0.0% |
BlockCopy      |   13 |  5.9452 ns | 0.0198 ns | 168,205,061.59 | -1.5% |
VectorizedCopy |   13 |  4.0097 ns | 0.0764 ns | 249,478,943.34 | 46.2% |
ArrayCopy      |   14 |  5.8726 ns | 0.1039 ns | 170,332,872.65 | 0.0% |
BlockCopy      |   14 |  6.0807 ns | 0.1034 ns | 164,500,497.80 | -3.4% |
VectorizedCopy |   14 |  4.3579 ns | 0.0741 ns | 229,535,593.28 | 34.8% |
ArrayCopy      |   15 |  5.9407 ns | 0.1045 ns | 168,380,987.89 | 0.0% |
BlockCopy      |   15 |  6.0864 ns | 0.1013 ns | 164,346,271.10 | -2.4% |
VectorizedCopy |   15 |  4.6881 ns | 0.0691 ns | 213,349,804.77 | 26.7% |
ArrayCopy      |   16 |  5.7952 ns | 0.1034 ns | 172,611,407.93 | 0.0% |
BlockCopy      |   16 |  5.9432 ns | 0.1025 ns | 168,307,450.63 | -2.5% |
VectorizedCopy |   16 |  3.8084 ns | 0.0088 ns | 262,578,647.13 | 52.1% |
ArrayCopy      |   17 | 10.6954 ns | 0.0050 ns |  93,498,530.53 | 0.0% |
BlockCopy      |   17 | 10.7207 ns | 0.1785 ns |  93,303,079.33 | -0.2% |
VectorizedCopy |   17 |  4.1017 ns | 0.0030 ns | 243,800,116.81 | 160.8% |
ArrayCopy      |   18 | 10.5726 ns | 0.1837 ns |  94,611,697.63 | 0.0% |
BlockCopy      |   18 | 10.7337 ns | 0.1975 ns |  93,195,517.60 | -1.5% |
VectorizedCopy |   18 |  4.2452 ns | 0.0136 ns | 235,559,959.02 | 149.0% |
ArrayCopy      |   19 | 10.6961 ns | 0.0099 ns |  93,492,317.08 | 0.0% |
BlockCopy      |   19 | 10.5863 ns | 0.1980 ns |  94,493,228.69 | 1.1% |
VectorizedCopy |   19 |  4.7175 ns | 0.2517 ns | 212,539,091.31 | 127.3% |
ArrayCopy      |   20 | 10.9211 ns | 0.8896 ns |  92,124,670.55 | 0.0% |
BlockCopy      |   20 | 10.8452 ns | 0.0090 ns |  92,206,820.56 | 0.1% |
VectorizedCopy |   20 |  3.9598 ns | 0.0051 ns | 252,538,656.81 | 174.1% |
ArrayCopy      |   21 | 10.5724 ns | 0.1783 ns |  94,611,974.25 | 0.0% |
BlockCopy      |   21 | 10.8392 ns | 0.0055 ns |  92,257,363.86 | -2.5% |
VectorizedCopy |   21 |  4.2442 ns | 0.0065 ns | 235,617,715.97 | 149.0% |
ArrayCopy      |   22 | 10.6972 ns | 0.0124 ns |  93,482,939.16 | 0.0% |
BlockCopy      |   22 | 10.4680 ns | 0.0311 ns |  95,530,367.23 | 2.2% |
VectorizedCopy |   22 |  4.5098 ns | 0.0951 ns | 221,833,862.72 | 137.3% |
ArrayCopy      |   23 | 10.5796 ns | 0.1825 ns |  94,548,608.52 | 0.0% |
BlockCopy      |   23 | 10.5863 ns | 0.1857 ns |  94,489,275.32 | -0.1% |
VectorizedCopy |   23 |  4.6925 ns | 0.0170 ns | 213,106,580.99 | 125.4% |
ArrayCopy      |   24 | 10.7015 ns | 0.0256 ns |  93,445,585.62 | 0.0% |
BlockCopy      |   24 | 10.8423 ns | 0.0068 ns |  92,231,634.97 | -1.3% |
VectorizedCopy |   24 |  3.8208 ns | 0.0251 ns | 261,736,065.75 | 180.1% |
ArrayCopy      |   25 | 10.5770 ns | 0.1734 ns |  94,569,267.61 | 0.0% |
BlockCopy      |   25 | 10.4676 ns | 0.0376 ns |  95,533,654.76 | 1.0% |
VectorizedCopy |   25 |  4.0966 ns | 0.0753 ns | 244,184,056.13 | 158.2% |
ArrayCopy      |   26 | 10.8758 ns | 0.5318 ns |  92,156,372.85 | 0.0% |
BlockCopy      |   26 | 10.5921 ns | 0.1797 ns |  94,436,119.76 | 2.5% |
VectorizedCopy |   26 |  4.3465 ns | 0.0769 ns | 230,139,687.68 | 149.7% |
ArrayCopy      |   27 | 10.4394 ns | 0.1844 ns |  95,819,737.35 | 0.0% |
BlockCopy      |   27 | 10.8496 ns | 0.0193 ns |  92,169,376.78 | -3.8% |
VectorizedCopy |   27 |  4.4507 ns | 0.0814 ns | 224,754,784.46 | 134.6% |
ArrayCopy      |   28 | 10.7901 ns | 0.4255 ns |  92,815,804.65 | 0.0% |
BlockCopy      |   28 | 10.8418 ns | 0.0038 ns |  92,235,904.69 | -0.6% |
VectorizedCopy |   28 |  4.1366 ns | 0.0478 ns | 241,772,377.39 | 160.5% |
ArrayCopy      |   29 | 10.5736 ns | 0.1783 ns |  94,601,907.58 | 0.0% |
BlockCopy      |   29 | 10.5867 ns | 0.1856 ns |  94,486,179.54 | -0.1% |
VectorizedCopy |   29 |  4.1551 ns | 0.0704 ns | 240,733,052.56 | 154.5% |
ArrayCopy      |   30 | 10.6983 ns | 0.0190 ns |  93,473,071.47 | 0.0% |
BlockCopy      |   30 | 10.8461 ns | 0.0249 ns |  92,199,634.98 | -1.4% |
VectorizedCopy |   30 |  4.7840 ns | 0.0932 ns | 209,105,993.58 | 123.7% |
ArrayCopy      |   31 | 10.5758 ns | 0.1861 ns |  94,583,717.64 | 0.0% |
BlockCopy      |   31 | 10.8505 ns | 0.0259 ns |  92,161,874.17 | -2.6% |
VectorizedCopy |   31 |  4.8411 ns | 0.0221 ns | 206,569,087.60 | 118.4% |
ArrayCopy      |   32 | 10.5688 ns | 0.1852 ns |  94,646,552.93 | 0.0% |
BlockCopy      |   32 | 10.4672 ns | 0.0402 ns |  95,537,677.55 | 0.9% |
VectorizedCopy |   32 |  2.7518 ns | 0.0500 ns | 363,515,306.64 | 284.1% |
ArrayCopy      |   33 | 10.5647 ns | 0.1977 ns |  94,687,082.77 | 0.0% |
BlockCopy      |   33 | 10.5894 ns | 0.1819 ns |  94,461,058.17 | -0.2% |
VectorizedCopy |   33 |  4.1079 ns | 0.0114 ns | 243,435,478.42 | 157.1% |
ArrayCopy      |   34 | 10.6961 ns | 0.0034 ns |  93,492,194.97 | 0.0% |
BlockCopy      |   34 | 10.8399 ns | 0.0041 ns |  92,252,082.38 | -1.3% |
VectorizedCopy |   34 |  4.3481 ns | 0.0730 ns | 230,046,570.17 | 146.1% |
ArrayCopy      |   35 | 10.6989 ns | 0.0191 ns |  93,467,991.23 | 0.0% |
BlockCopy      |   35 | 10.7163 ns | 0.1887 ns |  93,343,696.60 | -0.1% |
VectorizedCopy |   35 |  4.4708 ns | 0.0788 ns | 223,741,943.23 | 139.4% |
ArrayCopy      |   36 | 10.6876 ns | 0.3019 ns |  93,639,068.50 | 0.0% |
BlockCopy      |   36 | 10.8447 ns | 0.0251 ns |  92,211,290.49 | -1.5% |
VectorizedCopy |   36 |  4.0572 ns | 0.0692 ns | 246,548,152.20 | 163.3% |
ArrayCopy      |   37 | 11.0047 ns | 0.7339 ns |  91,248,906.70 | 0.0% |
BlockCopy      |   37 | 10.8497 ns | 0.0380 ns |  92,169,925.93 | 1.0% |
VectorizedCopy |   37 |  4.2041 ns | 0.0736 ns | 237,933,398.49 | 160.8% |
ArrayCopy      |   38 | 10.5690 ns | 0.1822 ns |  94,643,529.65 | 0.0% |
BlockCopy      |   38 | 10.8497 ns | 0.0273 ns |  92,168,736.47 | -2.6% |
VectorizedCopy |   38 |  4.4621 ns | 0.0773 ns | 224,173,089.50 | 136.9% |
ArrayCopy      |   39 | 10.4477 ns | 0.1792 ns |  95,742,065.87 | 0.0% |
BlockCopy      |   39 | 10.5826 ns | 0.1863 ns |  94,522,324.65 | -1.3% |
VectorizedCopy |   39 |  4.8572 ns | 0.0111 ns | 205,880,173.08 | 115.0% |
ArrayCopy      |   40 | 10.5745 ns | 0.1778 ns |  94,593,115.63 | 0.0% |
BlockCopy      |   40 | 10.7357 ns | 0.1674 ns |  93,169,449.07 | -1.5% |
VectorizedCopy |   40 |  4.0031 ns | 0.0755 ns | 249,893,099.40 | 164.2% |
ArrayCopy      |   41 | 10.7052 ns | 0.0403 ns |  93,414,127.26 | 0.0% |
BlockCopy      |   41 | 10.7163 ns | 0.1880 ns |  93,344,062.30 | -0.1% |
VectorizedCopy |   41 |  4.3465 ns | 0.0780 ns | 230,143,660.37 | 146.4% |
ArrayCopy      |   42 | 10.4468 ns | 0.1874 ns |  95,752,896.34 | 0.0% |
BlockCopy      |   42 | 10.4636 ns | 0.0292 ns |  95,570,271.75 | -0.2% |
VectorizedCopy |   42 |  4.6383 ns | 0.0817 ns | 215,662,815.73 | 125.2% |
ArrayCopy      |   43 | 10.4503 ns | 0.1812 ns |  95,718,238.83 | 0.0% |
BlockCopy      |   43 | 10.5768 ns | 0.1914 ns |  94,575,828.67 | -1.2% |
VectorizedCopy |   43 |  4.7315 ns | 0.0853 ns | 211,413,378.48 | 120.9% |
ArrayCopy      |   44 | 10.6974 ns | 0.0251 ns |  93,480,731.60 | 0.0% |
BlockCopy      |   44 | 10.8399 ns | 0.0074 ns |  92,251,738.01 | -1.3% |
VectorizedCopy |   44 |  4.4153 ns | 0.0125 ns | 226,489,223.32 | 142.3% |
ArrayCopy      |   45 | 10.4417 ns | 0.1816 ns |  95,797,298.71 | 0.0% |
BlockCopy      |   45 | 10.7124 ns | 0.1849 ns |  93,376,649.25 | -2.5% |
VectorizedCopy |   45 |  4.4386 ns | 0.0768 ns | 225,361,446.43 | 135.2% |
ArrayCopy      |   46 | 10.5716 ns | 0.1853 ns |  94,621,194.04 | 0.0% |
BlockCopy      |   46 | 10.5959 ns | 0.1766 ns |  94,400,935.04 | -0.2% |
VectorizedCopy |   46 |  4.8211 ns | 0.1021 ns | 207,513,219.66 | 119.3% |
ArrayCopy      |   47 | 10.5774 ns | 0.1930 ns |  94,572,136.25 | 0.0% |
BlockCopy      |   47 | 10.7115 ns | 0.1838 ns |  93,384,868.72 | -1.3% |
VectorizedCopy |   47 |  5.0163 ns | 0.0902 ns | 199,412,917.35 | 110.9% |
ArrayCopy      |   48 | 10.7236 ns | 0.0447 ns |  93,254,055.87 | 0.0% |
BlockCopy      |   48 | 10.5947 ns | 0.1771 ns |  94,412,237.25 | 1.2% |
VectorizedCopy |   48 |  4.1953 ns | 0.0739 ns | 238,430,563.58 | 155.7% |
ArrayCopy      |   49 | 10.6932 ns | 0.0230 ns |  93,517,462.00 | 0.0% |
BlockCopy      |   49 | 10.8352 ns | 0.0184 ns |  92,291,977.47 | -1.3% |
VectorizedCopy |   49 |  4.4882 ns | 0.0854 ns | 222,883,635.31 | 138.3% |
ArrayCopy      |   50 | 10.5692 ns | 0.1829 ns |  94,641,893.59 | 0.0% |
BlockCopy      |   50 | 10.5813 ns | 0.1775 ns |  94,531,829.34 | -0.1% |
VectorizedCopy |   50 |  4.7818 ns | 0.0843 ns | 209,190,309.78 | 121.0% |
ArrayCopy      |   51 | 10.7062 ns | 0.0437 ns |  93,405,272.22 | 0.0% |
BlockCopy      |   51 | 10.7144 ns | 0.1858 ns |  93,360,033.35 | 0.0% |
VectorizedCopy |   51 |  4.8948 ns | 0.0903 ns | 204,363,940.83 | 118.8% |
ArrayCopy      |   52 | 10.5718 ns | 0.1752 ns |  94,616,953.99 | 0.0% |
BlockCopy      |   52 | 10.7187 ns | 0.1882 ns |  93,322,595.59 | -1.4% |
VectorizedCopy |   52 |  4.4951 ns | 0.0806 ns | 222,536,108.01 | 135.2% |
ArrayCopy      |   53 | 10.5780 ns | 0.1920 ns |  94,565,822.23 | 0.0% |
BlockCopy      |   53 | 10.7211 ns | 0.1888 ns |  93,301,972.95 | -1.3% |
VectorizedCopy |   53 |  4.6009 ns | 0.0692 ns | 217,394,557.63 | 129.9% |
ArrayCopy      |   54 | 10.5780 ns | 0.1817 ns |  94,562,802.38 | 0.0% |
BlockCopy      |   54 | 10.5926 ns | 0.1811 ns |  94,432,281.42 | -0.1% |
VectorizedCopy |   54 |  5.1696 ns | 0.0218 ns | 193,440,403.83 | 104.6% |
ArrayCopy      |   55 | 10.6973 ns | 0.0265 ns |  93,481,684.14 | 0.0% |
BlockCopy      |   55 | 10.5832 ns | 0.1808 ns |  94,515,680.56 | 1.1% |
VectorizedCopy |   55 |  5.1892 ns | 0.0957 ns | 192,769,676.39 | 106.2% |
ArrayCopy      |   56 | 10.4442 ns | 0.1845 ns |  95,775,804.66 | 0.0% |
BlockCopy      |   56 | 10.5789 ns | 0.1899 ns |  94,557,038.50 | -1.3% |
VectorizedCopy |   56 |  4.2280 ns | 0.0667 ns | 236,577,786.86 | 147.0% |
ArrayCopy      |   57 | 10.5703 ns | 0.1817 ns |  94,631,762.09 | 0.0% |
BlockCopy      |   57 | 10.8406 ns | 0.0118 ns |  92,245,716.72 | -2.5% |
VectorizedCopy |   57 |  4.5466 ns | 0.0085 ns | 219,947,200.56 | 132.4% |
ArrayCopy      |   58 | 10.4470 ns | 0.1809 ns |  95,748,944.26 | 0.0% |
BlockCopy      |   58 | 10.8512 ns | 0.0346 ns |  92,156,821.13 | -3.8% |
VectorizedCopy |   58 |  4.7337 ns | 0.0838 ns | 211,314,153.63 | 120.7% |
ArrayCopy      |   59 | 10.7050 ns | 0.0391 ns |  93,415,428.11 | 0.0% |
BlockCopy      |   59 | 10.5863 ns | 0.1832 ns |  94,488,878.88 | 1.1% |
VectorizedCopy |   59 |  4.9437 ns | 0.0897 ns | 202,344,005.21 | 116.6% |
ArrayCopy      |   60 | 10.5793 ns | 0.1928 ns |  94,554,805.56 | 0.0% |
BlockCopy      |   60 | 10.7174 ns | 0.1793 ns |  93,331,749.81 | -1.3% |
VectorizedCopy |   60 |  4.4475 ns | 0.0745 ns | 224,906,136.13 | 137.9% |
ArrayCopy      |   61 | 10.4488 ns | 0.1788 ns |  95,731,248.77 | 0.0% |
BlockCopy      |   61 | 10.8450 ns | 0.0217 ns |  92,208,754.57 | -3.7% |
VectorizedCopy |   61 |  4.6320 ns | 0.0864 ns | 215,963,550.98 | 125.6% |
ArrayCopy      |   62 | 10.5717 ns | 0.1841 ns |  94,620,595.15 | 0.0% |
BlockCopy      |   62 | 10.4619 ns | 0.0358 ns |  95,585,758.60 | 1.0% |
VectorizedCopy |   62 |  5.2046 ns | 0.1126 ns | 192,222,130.27 | 103.2% |
ArrayCopy      |   63 | 10.4454 ns | 0.1879 ns |  95,765,084.88 | 0.0% |
BlockCopy      |   63 | 10.4667 ns | 0.0428 ns |  95,543,050.87 | -0.2% |
VectorizedCopy |   63 |  5.4780 ns | 0.1084 ns | 182,617,306.37 | 90.7% |
ArrayCopy      |   64 | 10.5678 ns | 0.1831 ns |  94,655,282.22 | 0.0% |
BlockCopy      |   64 | 10.5859 ns | 0.1862 ns |  94,493,147.64 | -0.2% |
VectorizedCopy |   64 |  3.0431 ns | 0.0535 ns | 328,707,328.52 | 247.3% |
ArrayCopy      |   65 | 11.7228 ns | 0.0219 ns |  85,304,264.61 | 0.0% |
BlockCopy      |   65 | 11.8723 ns | 0.0283 ns |  84,229,978.54 | -1.3% |
VectorizedCopy |   65 |  4.5469 ns | 0.0092 ns | 219,930,039.03 | 157.8% |
ArrayCopy      |   66 | 11.9697 ns | 0.3624 ns |  83,616,982.01 | 0.0% |
BlockCopy      |   66 | 11.7353 ns | 0.2108 ns |  85,239,892.37 | 1.9% |
VectorizedCopy |   66 |  4.8178 ns | 0.0909 ns | 207,635,742.42 | 148.3% |
ArrayCopy      |   67 | 11.5845 ns | 0.1968 ns |  86,346,298.55 | 0.0% |
BlockCopy      |   67 | 11.4568 ns | 0.0379 ns |  87,285,076.48 | 1.1% |
VectorizedCopy |   67 |  4.9234 ns | 0.0831 ns | 203,167,583.05 | 135.3% |
ArrayCopy      |   68 | 11.8114 ns | 0.4577 ns |  84,785,635.03 | 0.0% |
BlockCopy      |   68 | 11.5943 ns | 0.2004 ns |  86,273,706.19 | 1.8% |
VectorizedCopy |   68 |  4.4926 ns | 0.0790 ns | 222,656,588.79 | 162.6% |
ArrayCopy      |   69 | 11.4554 ns | 0.2026 ns |  87,321,347.35 | 0.0% |
BlockCopy      |   69 | 11.5861 ns | 0.1985 ns |  86,334,350.84 | -1.1% |
VectorizedCopy |   69 |  4.6350 ns | 0.0807 ns | 215,812,211.93 | 147.1% |
ArrayCopy      |   70 | 11.8102 ns | 0.4666 ns |  84,798,959.52 | 0.0% |
BlockCopy      |   70 | 11.8693 ns | 0.0065 ns |  84,250,770.18 | -0.6% |
VectorizedCopy |   70 |  4.9396 ns | 0.0953 ns | 202,520,153.89 | 138.8% |
ArrayCopy      |   71 | 11.5864 ns | 0.1984 ns |  86,332,524.56 | 0.0% |
BlockCopy      |   71 | 11.8671 ns | 0.0091 ns |  84,266,549.13 | -2.4% |
VectorizedCopy |   71 |  5.3640 ns | 0.2244 ns | 186,738,793.42 | 116.3% |
ArrayCopy      |   72 | 11.5907 ns | 0.2111 ns |  86,303,703.63 | 0.0% |
BlockCopy      |   72 | 11.7275 ns | 0.1998 ns |  85,294,205.42 | -1.2% |
VectorizedCopy |   72 |  4.4905 ns | 0.0767 ns | 222,755,376.45 | 158.1% |
ArrayCopy      |   73 | 11.4483 ns | 0.2003 ns |  87,375,028.12 | 0.0% |
BlockCopy      |   73 | 11.5806 ns | 0.2065 ns |  86,377,472.66 | -1.1% |
VectorizedCopy |   73 |  4.8654 ns | 0.0037 ns | 205,535,085.88 | 135.2% |
ArrayCopy      |   74 | 11.3411 ns | 0.1116 ns |  88,182,845.80 | 0.0% |
BlockCopy      |   74 | 11.8910 ns | 0.0604 ns |  84,099,213.47 | -4.6% |
VectorizedCopy |   74 |  5.0741 ns | 0.0861 ns | 197,135,881.15 | 123.6% |
ArrayCopy      |   75 | 11.4545 ns | 0.2026 ns |  87,328,072.43 | 0.0% |
BlockCopy      |   75 | 11.5902 ns | 0.2031 ns |  86,305,271.44 | -1.2% |
VectorizedCopy |   75 |  5.2956 ns | 0.0147 ns | 188,838,550.18 | 116.2% |
ArrayCopy      |   76 | 11.5887 ns | 0.2026 ns |  86,316,457.54 | 0.0% |
BlockCopy      |   76 | 11.5931 ns | 0.2004 ns |  86,283,200.57 | 0.0% |
VectorizedCopy |   76 |  4.8211 ns | 0.0843 ns | 207,483,796.86 | 140.4% |
ArrayCopy      |   77 | 11.4720 ns | 0.2376 ns |  87,204,046.68 | 0.0% |
BlockCopy      |   77 | 11.7353 ns | 0.1891 ns |  85,234,653.29 | -2.3% |
VectorizedCopy |   77 |  4.9858 ns | 0.0089 ns | 200,571,183.57 | 130.0% |
ArrayCopy      |   78 | 11.7347 ns | 0.0494 ns |  85,219,009.93 | 0.0% |
BlockCopy      |   78 | 11.8795 ns | 0.0483 ns |  84,180,198.21 | -1.2% |
VectorizedCopy |   78 |  5.2029 ns | 0.0920 ns | 192,257,348.72 | 125.6% |
ArrayCopy      |   79 | 11.6882 ns | 0.5285 ns |  85,720,801.91 | 0.0% |
BlockCopy      |   79 | 11.5858 ns | 0.2075 ns |  86,338,958.09 | 0.7% |
VectorizedCopy |   79 |  5.5101 ns | 0.0941 ns | 181,535,547.31 | 111.8% |
ArrayCopy      |   80 | 11.7256 ns | 0.0269 ns |  85,284,072.64 | 0.0% |
BlockCopy      |   80 | 11.8799 ns | 0.0347 ns |  84,176,734.58 | -1.3% |
VectorizedCopy |   80 |  4.7317 ns | 0.0028 ns | 211,342,207.44 | 147.8% |
ArrayCopy      |   81 | 11.5829 ns | 0.2011 ns |  86,359,655.13 | 0.0% |
BlockCopy      |   81 | 11.5920 ns | 0.2006 ns |  86,291,417.05 | -0.1% |
VectorizedCopy |   81 |  4.9856 ns | 0.0143 ns | 200,577,991.64 | 132.3% |
ArrayCopy      |   82 | 11.3037 ns | 0.0113 ns |  88,466,615.40 | 0.0% |
BlockCopy      |   82 | 11.5961 ns | 0.1988 ns |  86,260,292.05 | -2.5% |
VectorizedCopy |   82 |  5.1768 ns | 0.0903 ns | 193,224,001.89 | 118.4% |
ArrayCopy      |   83 | 11.7239 ns | 0.0249 ns |  85,296,328.66 | 0.0% |
BlockCopy      |   83 | 11.7252 ns | 0.1906 ns |  85,308,585.31 | 0.0% |
VectorizedCopy |   83 |  5.3646 ns | 0.0902 ns | 186,458,199.54 | 118.6% |
ArrayCopy      |   84 | 12.0557 ns | 0.4690 ns |  83,066,472.97 | 0.0% |
BlockCopy      |   84 | 11.7439 ns | 0.1928 ns |  85,173,036.45 | 2.5% |
VectorizedCopy |   84 |  4.9220 ns | 0.0777 ns | 203,218,805.30 | 144.6% |
ArrayCopy      |   85 | 11.5402 ns | 0.1966 ns |  86,677,798.64 | 0.0% |
BlockCopy      |   85 | 11.7288 ns | 0.2060 ns |  85,285,625.32 | -1.6% |
VectorizedCopy |   85 |  5.1682 ns | 0.0171 ns | 193,491,532.67 | 123.2% |
ArrayCopy      |   86 | 11.5886 ns | 0.2068 ns |  86,318,315.35 | 0.0% |
BlockCopy      |   86 | 11.5933 ns | 0.1974 ns |  86,280,486.51 | 0.0% |
VectorizedCopy |   86 |  5.4244 ns | 0.0042 ns | 184,351,617.69 | 113.6% |
ArrayCopy      |   87 | 11.7222 ns | 0.0283 ns |  85,309,030.14 | 0.0% |
BlockCopy      |   87 | 11.4522 ns | 0.0269 ns |  87,319,971.53 | 2.4% |
VectorizedCopy |   87 |  5.5766 ns | 0.0981 ns | 179,372,766.90 | 110.3% |
ArrayCopy      |   88 | 11.5825 ns | 0.2005 ns |  86,362,283.55 | 0.0% |
BlockCopy      |   88 | 11.5944 ns | 0.2000 ns |  86,273,090.63 | -0.1% |
VectorizedCopy |   88 |  4.6403 ns | 0.0779 ns | 215,562,830.79 | 149.6% |
ArrayCopy      |   89 | 11.7236 ns | 0.0282 ns |  85,298,209.63 | 0.0% |
BlockCopy      |   89 | 11.7365 ns | 0.2128 ns |  85,231,275.31 | -0.1% |
VectorizedCopy |   89 |  5.0037 ns | 0.0122 ns | 199,853,963.53 | 134.3% |
ArrayCopy      |   90 | 12.2382 ns | 0.7389 ns |  81,988,480.82 | 0.0% |
BlockCopy      |   90 | 11.7272 ns | 0.2040 ns |  85,296,857.13 | 4.0% |
VectorizedCopy |   90 |  5.2192 ns | 0.0945 ns | 191,659,846.71 | 133.8% |
ArrayCopy      |   91 | 11.5870 ns | 0.2036 ns |  86,329,527.94 | 0.0% |
BlockCopy      |   91 | 11.8701 ns | 0.0259 ns |  84,245,973.81 | -2.4% |
VectorizedCopy |   91 |  5.4400 ns | 0.0114 ns | 183,825,457.11 | 112.9% |
ArrayCopy      |   92 | 11.5892 ns | 0.2083 ns |  86,314,606.59 | 0.0% |
BlockCopy      |   92 | 11.5953 ns | 0.2023 ns |  86,266,592.15 | -0.1% |
VectorizedCopy |   92 |  5.0319 ns | 0.0077 ns | 198,730,867.64 | 130.2% |
ArrayCopy      |   93 | 11.5892 ns | 0.1958 ns |  86,311,165.58 | 0.0% |
BlockCopy      |   93 | 11.8618 ns | 0.0193 ns |  84,304,339.20 | -2.3% |
VectorizedCopy |   93 |  5.0710 ns | 0.0884 ns | 197,258,274.48 | 128.5% |
ArrayCopy      |   94 | 11.7264 ns | 0.0237 ns |  85,277,766.73 | 0.0% |
BlockCopy      |   94 | 11.7355 ns | 0.1975 ns |  85,235,405.64 | 0.0% |
VectorizedCopy |   94 |  5.6256 ns | 0.1023 ns | 177,817,567.19 | 108.5% |
ArrayCopy      |   95 | 11.7331 ns | 0.0535 ns |  85,230,760.17 | 0.0% |
BlockCopy      |   95 | 11.5949 ns | 0.2184 ns |  86,273,792.82 | 1.2% |
VectorizedCopy |   95 |  5.8273 ns | 0.0056 ns | 171,606,888.66 | 101.3% |
ArrayCopy      |   96 | 11.3091 ns | 0.1958 ns |  88,449,832.87 | 0.0% |
BlockCopy      |   96 | 11.7332 ns | 0.0389 ns |  85,229,336.97 | -3.6% |
VectorizedCopy |   96 |  3.6662 ns | 0.0124 ns | 272,761,616.16 | 208.4% |
ArrayCopy      |  508 | 13.3261 ns | 0.2335 ns |  75,062,999.88 | 0.0% |
BlockCopy      |  508 | 13.3132 ns | 0.2298 ns |  75,134,983.27 | 0.1% |
VectorizedCopy |  508 | 11.6263 ns | 0.0577 ns |  86,013,569.89 | 14.6% |
ArrayCopy      |  509 | 13.3366 ns | 0.2260 ns |  75,002,805.45 | 0.0% |
BlockCopy      |  509 | 13.5102 ns | 0.2664 ns |  74,046,033.00 | -1.3% |
VectorizedCopy |  509 | 11.7425 ns | 0.0397 ns |  85,161,359.30 | 13.5% |
ArrayCopy      |  510 | 13.0299 ns | 0.0439 ns |  76,747,285.21 | 0.0% |
BlockCopy      |  510 | 13.4774 ns | 0.2236 ns |  74,218,375.69 | -3.3% |
VectorizedCopy |  510 | 12.2528 ns | 0.0306 ns |  81,614,566.00 | 6.3% |
ArrayCopy      |  511 | 14.1144 ns | 0.8999 ns |  71,116,990.45 | 0.0% |
BlockCopy      |  511 | 13.6376 ns | 0.0369 ns |  73,327,102.71 | 3.1% |
VectorizedCopy |  511 | 12.0609 ns | 0.0608 ns |  82,914,377.74 | 16.6% |
ArrayCopy      |  512 | 13.4078 ns | 0.2899 ns |  74,617,647.48 | 0.0% |
BlockCopy      |  512 | 13.6296 ns | 0.0080 ns |  73,370,014.01 | -1.7% |
VectorizedCopy |  512 | 10.0166 ns | 0.1897 ns |  99,869,525.44 | 33.8% |
ArrayCopy      |  513 | 13.6352 ns | 0.0327 ns |  73,339,879.75 | 0.0% |
BlockCopy      |  513 | 13.6228 ns | 0.2450 ns |  73,429,789.82 | 0.1% |
VectorizedCopy |  513 | 11.9370 ns | 0.0212 ns |  83,773,574.83 | 14.2% |
ArrayCopy      |  543 | 13.1517 ns | 0.0325 ns |  76,036,487.57 | 0.0% |
BlockCopy      |  543 | 13.6717 ns | 0.1617 ns |  73,153,940.72 | -3.8% |
VectorizedCopy |  543 | 13.2079 ns | 0.0309 ns |  75,712,405.37 | -0.4% |
ArrayCopy      |  544 | 13.6455 ns | 0.0555 ns |  73,285,458.30 | 0.0% |
BlockCopy      |  544 | 13.5850 ns | 0.2165 ns |  73,629,017.73 | 0.5% |
VectorizedCopy |  544 | 11.0542 ns | 0.0313 ns |  90,463,832.62 | 23.4% |
ArrayCopy      |  547 | 13.4514 ns | 0.2285 ns |  74,362,264.10 | 0.0% |
BlockCopy      |  547 | 14.8555 ns | 0.7506 ns |  67,482,743.68 | -9.3% |
VectorizedCopy |  547 | 12.8129 ns | 0.0475 ns |  78,047,153.57 | 5.0% |
ArrayCopy      |  576 | 13.9691 ns | 0.6331 ns |  71,726,838.89 | 0.0% |
BlockCopy      |  576 | 13.7897 ns | 0.2525 ns |  72,541,555.67 | 1.1% |
VectorizedCopy |  576 | 11.0111 ns | 0.2011 ns |  90,846,551.92 | 26.7% |
ArrayCopy      | 1024 | 17.1668 ns | 0.8558 ns |  58,387,687.56 | 0.0% |
BlockCopy      | 1024 | 16.6559 ns | 0.2813 ns |  60,055,379.14 | 2.9% |
VectorizedCopy | 1024 | 17.6044 ns | 0.1651 ns |  56,808,897.59 | -2.7% |
ArrayCopy      | 4096 | 45.7931 ns | 1.6124 ns |  21,863,405.86 | 0.0% |
BlockCopy      | 4096 | 45.8172 ns | 1.2883 ns |  21,842,594.11 | -0.1% |
VectorizedCopy | 4096 | 47.0823 ns | 0.8151 ns |  21,245,586.44 | -2.8% |
ArrayCopy      | 8512 | 86.3654 ns | 0.3260 ns |  11,578,867.96 | 0.0% |
BlockCopy      | 8512 | 86.4953 ns | 0.3797 ns |  11,561,535.36 | -0.1% |
VectorizedCopy | 8512 | 92.2227 ns | 0.4168 ns |  10,843,528.27 | -6.4% |
