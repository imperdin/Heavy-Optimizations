First things fist, one of the bottle necks in Heavy is the core more than the HBM (As we currently don't have any public tool that unlocks the straps) and allow Vegas to break the 4 KH/s barrier


How to do so, the easy H/s boost on Vega 64 is to lower the watt draw first which is the V56 Bios that have the tighter straps and lower watts for the HBM that allows the vega to shift more power to the core at the same watt draw,
so this is goal #1 if you don't have a V56 is to get the V56 Bios and flash into it (Do so at your own risk)


Step #2 another easy 3-4% efficiency tweak (on V56/V64) is to "optimize" the memory a little bit, which can be done by a technique I called foreplay
How it's done, first you initialize a foreplay miner for 15s to warm up the HBM for the miner, it can be done using one of the two options


A) Easy, use Cast-xmr --intensity 10 option to mine for 30 seconds at your pool of choice


B) Medium, use a diffrent Heavy amd mining config calld warm.txt with the following threads for warm (V56 & V64)

{ "index" : 0, "intensity" : 896, "worksize" : 8, "affine_to_cpu" : false, "strided_index" : 2, "mem_chunk" : 16, "comp_mode" : true },  
{ "index" : 0, "intensity" : 960, "worksize" : 8, "affine_to_cpu" : false, "strided_index" : 2, "mem_chunk" : 16, "comp_mode" : true },
  
(For Rare Cases)
  
  
{ "index" : 0, "intensity" : 960, "worksize" : 8, "affine_to_cpu" : false, "strided_index" : 2, "mem_chunk" : 16, "comp_mode" : true },    
{ "index" : 0, "intensity" : 960, "worksize" : 8, "affine_to_cpu" : false, "strided_index" : 2, "mem_chunk" : 16, "comp_mode" : true },  

If it gives better results optimizations for the latter

And the last threads for standard mining 

{ "index" : 0, "intensity" : 896, "worksize" : 8, "affine_to_cpu" : false, "strided_index" : 2, "mem_chunk" : 16, "comp_mode" : true },
{ "index" : 0, "intensity" : 896, "worksize" : 8, "affine_to_cpu" : false, "strided_index" : 2, "mem_chunk" : 16, "comp_mode" : true },



Using the easy start tools

Start.bat
```
@echo off
powershell -command Start-Process C:\miner\Config.bat -Verb runAs
timeout /t 120 /NOBREAK
powershell -command Start-Process C:\miner\Monitor.exe
```


Config.bat
```cd C:\miner
timeout /t 5 /NOBREAK
devcon.exe disable "PCI\VEN_1002&DEV_687F"
timeout /t 10 /NOBREAK
devcon.exe enable "PCI\VEN_1002&DEV_687F"
timeout /t 5 /NOBREAK
OverdriveNTool.exe -p1Default -p2Default -p3Default -p4Default -p5Default -p6Default
timeout /t 5 /NOBREAK
powershell -command Start-Process C:\miner\warm.bat -Verb runAs
timeout /t 15 /NOBREAK
taskkill /f /im "xmr-stak.exe"
timeout /t 5 /NOBREAK
xmr-stak --noCPU --noNVIDIA
```



Warm.bat
```
cd C:\miner
xmr-stak --noCPU --noNVIDIA --amd amdwarm.txt
```





After using both parts 1 & 2 you should find youself wondering what's the H/s like
So without further optimizing the 1409@905 & 950%900 on a V56 you should see ~1500 H/s on a V56
~1550 H/s on a V64 and ~2300 H/s on FE (With the Compute switch)


The thing is that the V64  mine better on the tighter V56 strap while freeing the mv to really boost the core clock
that is needed to crush Heavy

So you will want to aim for 1600@1000 + Core and increase it as much as you can for maximum H/s on all
And memory you can also increase to as high as possible 915->1015 without breaking 915 mv as it's already greedy enough

Optional less greedy core is 1500@950 & 1450@925  but it will only hash ~1600 H/s on V56

Using that config should bring you to ~1700 H/s on V56 and ~1850 H/s on V64 and about x H/s on a FE if the core is high enough

While I'm going to automate all of that in my potential upcoming miner, I want to reduce that time necassry for the public info
to cover most of the optimization that the "Secret Optimizers" have already done


Optional,
I have a [PoC for a watchdog](https://github.com/imperdin/Auto-Restart-Rig) I made that is really reliable and managed my rigs 24/7 without failing  
You can use my [PPT for V56](https://raw.githubusercontent.com/imperdin/GPU-Configs/master/Vega56.reg) at your own risk

As noted by (Bry Guy#8913) using NVMe PCIe M.2 allow to gain a 3-4% boost when using Vegas with only 4GB ram due to page file bottlenecks e.g [Samsung 970 Evo](https://www.amazon.com/Samsung-970-EVO-250GB-MZ-V7E250BW/dp/B07BN5FJZQ)
