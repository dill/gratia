# rtw works with scalar p and phi

    Code
      round(withr::with_seed(seed = 34657, rtw(mu = runif(10, min = 0, max = 10), p = 1.5,
      phi = 1.1)), 4)
    Output
       [1] 23.5189  1.5611  0.2695  1.2626  2.4825  5.4061  5.1371  0.6378  2.8042
      [10]  5.9205

# rtw works with vector p and phi

    Code
      round(withr::with_seed(seed = 34657, rtw(mu = runif(10, min = 0, max = 10), p = runif(
        10, min = 1, max = 2), phi = runif(10, 1, 2))), 4)
    Output
       [1]  1.6761 26.0810  3.0301  0.7552  7.9348  4.8814 13.6470  3.2859  5.6785
      [10] 73.8936

# rtw fails with negative mu

    Code
      rtw(mu = -2, p = 1.1, phi = 1.1)
    Condition
      Error in `rtw()`:
      ! mean 'mu' must be non-negative

# rtw fails with p outside below range

    Code
      rtw(mu = 2, p = 1L, phi = 1.1)
    Condition
      Error in `rtw()`:
      ! 'p' must be in interval (1, 2)

# rtw fails with p outside above range

    Code
      rtw(mu = 2, p = 3L, phi = 1.1)
    Condition
      Error in `rtw()`:
      ! 'p' must be in interval (1, 2)

# rtw fails with phi negative

    Code
      rtw(mu = 2, p = 1.5, phi = -1)
    Condition
      Error in `rtw()`:
      ! scale parameter 'phi' must be positive

