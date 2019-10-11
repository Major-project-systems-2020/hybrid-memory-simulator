Hybrid-Memory-Simulator : MARSSX86 && NVMain && DRAMSim2 && HybridSim 
=====================================================================

Copyright 2015 Huazhong University of Science and Technology (cyjseagull @ 163.com)

Hybrid-Memory-Simulator is a simulation tool for x86-64 based computing system with hybrid memory system consisting of both non-volatile memory(such as SSD , PCM ) and dynamic random access memory. 

It is based on four independent open-source project: MARSSx86(http://www.marss86.org/~marss86/index.php/Home), NVMain(http://wiki.nvmain.org/) , DRAMsim2(https://github.com/jimstevens2001/DRAMSim2), NVDIMMSim(nvm simulator,but not very accurate , https://github.com/jimstevens2001/NVDIMMSim), HybridSim(https://github.com/jimstevens2001/HybridSim).Thanks for the hard working of people who had developed these simulators and made them open-source.  

System Requirements
-------------------
To compile Marss on your system, you will need following:
* 2.5GHz CPU with minimum 2GB RAM (4GB Preferred)
* Standard C++ compiler, like g++ or icc
* SCons tool for compiling Marss (Minimum version 1.2.0)
* SDL Development Libraries (Required for QEMU)

Patches
------------------
These patches are provided for original marss which does not support for NVMAIN, DRAMSim2 and HybridSim.
* `patch-config` : modify config files to example config files of hybrid-memory-simulator ;  
* `patch-hybrid-simulator` : modify files of original MARSSX86 , makes it support for NVMAIN, DRAMSim2 and HybridSim
* `attention`: before patch with file patch-config , you must backup your config files in the config directory first

commands:

    patches -p1 < path of file patch-config
    patches -p1 < path of file patch-hybrid-system


Compiling
---------
If you do not have SCons install, install it using your stanard application
installation program like apt-get or yum.

Once you have SCons install go to the code hybrid-memory-simulator and give following command:

    $scons -Q config=machine configuration path  [mem=NVMAIN/DRAMSIM/HYBRIDSIM] [mem_dir=path of memory simulator] [debug=0/1/2] [c=num of cores]

* config: required , path of machine config file
* mem : optional , default is `NVMAIN` ( plug NVMain as memory system simulator)
* mem_dir : optional , default is `hybrid-memory-simulator/nvmain` ( if mem_dir is default , make sure nvmain simulator exists in hybrid-memory-simulator directory)
* debug : optional , default is `0` (no debugging)
* c: optional , default is `1`, single-core configuration
* attention : 
	* `NVMAIN` as `mem` , default `mem_dir` is `hybrid-memory-simulator/nvmain`
	* `DRAMSIM` as `mem` , default `mem_dir` is `hybrid-memory-simulator/DRAMSim2`
	* `HYBRIDSIM` as `mem` , default `mem_dir` is `hybrid-memory-simulator/HybridSim`
	* before compile hybrid-memory-system , you must make sure `libdramsim.so` in `DRAMSim2 directory and libhybridsim.so` in `HybridSim directory` 

e.g.:

    $ scons -Q mem=NVMAIN config=config/new-machine/moesi.conf c=2
To clean your compilation:

    $ command of build a machine -c ( eg. scons -Q mem=NVMAIN config=config/new-machine/moesi.conf c=2 -c)
    $ rm -rf build/
    
Running
-------
After successfull compilation, to run hybrid-memory-simulator , go to hybrid-memory-simulator directory , then give the following command:

    $ qemu/qemu-system-x86_64 -m [memory_size] -hda [path-to-qemu-disk-image] -simconfig [simconfig file path] [-nographic]
* `-m`: optional , memory size , unit is `M` or `G` or `K` 
* `-hda` : required , path of qemu disk image 
* `-simconfig`: optional , path of simconfig file , simconfig file is used to config simulation , such as logfile,nvmain_config_path , loglevel , configuration related to dramsim2 , etc. You can know it deeply through MARSSX86 webpage(http://www.marss86.org/~marss86/index.php/Home). You can also switch to simulation mode , and set simconfig according to following step.
* `-nographic`: when connect remote server with shell , and run qemu through shell , should add this option to disable graphic requirement.

You can use all the regular QEMU command here, like start VM window in VNC give
vnc :10 etc.  Once the system is booted, you can switch to Monitor mode using
Ctrl-Alt-2 key and give following command to switch to simulation mode:

    `(qemu) simconfig -run -stopinsns 100m -stats [stats-file-name]`

To get the list of available simulation options give following command:

    `(qemu) simconfig`


    // suraj44


Here's what I did to test the ls command:
You need to figure out the machine 'name'. I was fortuitous in finding the machine name of the machine built using NVMain. I was trying different things out and tried building it with only one core (c=1). An error popped up saying ```Machine 'nvmain_test_shared_l3' requires minimum of 2 contexts while you are compiling with 1 contexts. Skipping this machine 'nvmain_test_shared_l3'. ```TODO: What is the actual way of finding machine names?
Once you boot the VM, enter the username and password 'root'. You'll find some `start_sim`, `stop_sim` and `kill_sim` binaries present. enter monitor mode using the ctrl+alt+2 shortcut
Enter the following command after which it will wait for a "run" commmand to start the simulation:

```(qemu) simconfig -machine nvmain_test_shared_l3```

Switch back to guest console mode using the ctrl+alt+1 shortcut.

To simulate the ls command, run the following:

```./start_sim ; ls ; ./stop_sim ; ./kill_sim```

CPU and other machine statistics will be dumped into `pltsim.log` (on host machine)

Memory specific statistics will be dumped into `ptlsim.logDRAM_buffer_nvmain.log`





It will print all the simulation options on STDOUT.

Happy Hacking.
