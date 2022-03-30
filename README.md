# WRF EM_LES

## Write Patterns
My current setting for em_les_shallow_convection testcase does simulation every "1 second" until 1 hour is reached. It also saves a backup file every "30 mins". File writes happens only when saving the backup files. 

For each backup file, NFMPI_PUT_VARA_\<type\> are called 177 times for 177 variable arrays. Dimensions for each array need not equal. Dimensions are recorded in [this_file](em_les_shallow_convection/total_dim).

In particular, one variable has dimension (100, 100, 1, 1). 
1. When using 8 mpi ranks, 8 ranks are devided using 2 (row) * 4 (col).
2. When using 16 mpi ranks, 16 ranks are devided using 4 (row) * 4 (col).
2. When using 32 mpi ranks, 32 ranks are devided using 4 (row) * 8 (col).

Three output files are expected (one at time `0`, one at `30 min` and one at time `1 hr`), but only 2 out files are seen. However, 3 sets of writes are seen, as is expected.


## NetCDF Write Patterns:
Data from all processes are passed to Rank0 thorugh MPI_Gatherv, then Rank0 will perform the writes.

## Timing Results PnetCDF 1
### testing param
1. test case: em_les
2. input file: "namelist.input"
3. 500x500 (x1 x1) grid size,  
4. simulation time = 30 s, 
5. writes happens every 5 sec (history_interval = 5 sec)
6. on ACFS machine
### result
1. 8 mpi processes:

    writing a file generally takes 0.58 sec

    computing time for one time step (1s) is around 5 sec

    [output folder here](em_les_500x500_8p_30stotal_5seach)

2. 4 mpi processes:

    writing a file generally takes 0.45 sec

    computing time for one time step (1s) is around 5 sec

    [output folder here](em_les_500x500_4p_30stotal_5seach)
3. 2 mpi processes:

    writing a file generally takes 0.42 sec

    computing time for one time step (1s) is around 6 sec

    [output folder here](em_les_500x500_2p_30stotal_5seach)

4. 1 mpi process:

    writing a file generally takes 0.6 sec

    computing time for one time step (1s) is around 9.5 sec

    [output folder here](em_les_500x500_1p_30stotal_5seach)

## Timing Results PnetCDF 2
For both NetCDF and PnetCDF, I see significant performance decrease when number of processes increases (>=16). When the number of processes is small, adding more processes increase the performance. For large number of processes, NetCDF seems to perform less worse than PnetCDF, probably due to NetCDF only uses Rank0 to write.

### testing param
1. test case: em_les
2. input file: "namelist.input_shalconv"
3. 100x100 (x1 x1) grid size,  
4. simulation time = 1 hour, 
5. writes happens every 1 hour (history_interval = 60 min)
6. on ACFS machine

### result
1. 1 process

    Timing for processing wrfinput file (stream 0) for domain        1:    0.02579 elapsed seconds
    
    Timing for **Writing** wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.03243 elapsed seconds
    
    ----------------------------------------
    (**Computation time for each time step (1 sec)**)
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.59666 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.56301 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.56600 elapsed seconds
2. 2 processes
    
    Timing for processing wrfinput file (stream 0) for domain        1:    0.02294 elapsed seconds
    
    Timing for **Writing** wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.02794 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.37074 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.36114 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.34758 elapsed seconds
3. 4 processes
    
    Timing for processing wrfinput file (stream 0) for domain        1:    0.03400 elapsed seconds

    Timing for **Writing** wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.04381 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.35797 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.31933 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.31751 elapsed seconds

2. 8 processes

    Timing for processing wrfinput file (stream 0) for domain        1:    0.04902 elapsed seconds

    Timing for **Writing** wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.06376 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.39573 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.32606 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.32892 elapsed seconds

2. 16 processes

    Timing for processing wrfinput file (stream 0) for domain        1:   27.75686 elapsed seconds

    Timing for **Writing** wrfout_d01_0001-01-01_00:00:00 for domain        1:   29.70341 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:   31.56730 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    1.72810 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    1.67742 elapsed seconds

    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:   79.48034 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    3.18665 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    3.23715 elapsed seconds

