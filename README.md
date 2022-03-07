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



## Timing Results PnetCDF
For both NetCDF and PnetCDF, I see significant performance decrease when number of processes increases (>=16). When the number of processes is small, adding more processes increase the performance. For large number of ranks, NetCDF seems to perform less worse than PnetCDF, probably due to NetCDF only uses Rank0 to write.

1. 1 rank

    Timing for processing wrfinput file (stream 0) for domain        1:    0.02579 elapsed seconds
    
    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.03243 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.59666 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.56301 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.56600 elapsed seconds
2. 2 rank
    
    Timing for processing wrfinput file (stream 0) for domain        1:    0.02294 elapsed seconds
    
    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.02794 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.37074 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.36114 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.34758 elapsed seconds
3. 4 rank
    
    Timing for processing wrfinput file (stream 0) for domain        1:    0.03400 elapsed seconds

    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.04381 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.35797 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.31933 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.31751 elapsed seconds

2. 8 rank

    Timing for processing wrfinput file (stream 0) for domain        1:    0.04902 elapsed seconds

    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.06376 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.39573 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.32606 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.32892 elapsed seconds

2. 16 rank

    Timing for processing wrfinput file (stream 0) for domain        1:   27.75686 elapsed seconds

    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:   29.70341 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:   31.56730 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    1.72810 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    1.67742 elapsed seconds

3. 32 rank
    
    Timing for processing wrfinput file (stream 0) for domain        1:   69.28307 elapsed seconds
    
    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:   75.98269 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:   79.48034 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    3.18665 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    3.23715 elapsed seconds

## Timing Results NetCDF

1. 1 rank

    Timing for processing wrfinput file (stream 0) for domain        1:    0.05878 elapsed seconds

    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.17618 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.80372 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.56959 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.58920 elapsed seconds

2. 2 rank

    Timing for processing wrfinput file (stream 0) for domain        1:    0.07662 elapsed seconds

    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.18828 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.55218 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.34760 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.36045 elapsed seconds

3. 4 rank

    Timing for processing wrfinput file (stream 0) for domain        1:    0.07919 elapsed seconds
    
    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.19939 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.51710 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.31546 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.31889 elapsed seconds

4. 8 rank
    
    Timing for processing wrfinput file (stream 0) for domain        1:    0.08208 elapsed seconds
    
    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    0.22595 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:    0.55147 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    0.32636 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    0.32736 elapsed seconds

5. 16 rank

    Timing for processing wrfinput file (stream 0) for domain        1:    4.28982 elapsed seconds
    
    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:    9.75381 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:   11.12410 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    1.49696 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    1.39511 elapsed seconds

6. 32 rank

    Timing for processing wrfinput file (stream 0) for domain        1:   12.33482 elapsed seconds
    
    Timing for Writing wrfout_d01_0001-01-01_00:00:00 for domain        1:   32.74051 elapsed seconds
    
    ----------------------------------------
    
    Timing for main: time 0001-01-01_00:00:01 on domain   1:   36.37962 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:02 on domain   1:    3.11535 elapsed seconds
    
    Timing for main: time 0001-01-01_00:00:03 on domain   1:    3.24868 elapsed seconds
