# gratia 0.7.3.1 (In development)

## Bug fixes

* `draw.posterior_smooths` now plots posterior samples with a fixed aspect ratio
  if the smooth is isotropic. #148

# gratia 0.7.3

## User visible changes

* Plots of smooths now use "Partial effect" for the y-axis label in place of
  "Effect", to better indicate what is displayed.

## New features

* `confint.fderiv()` and `confint.gam()` now return their results as a tibble
  instead of a common-or-garden data frame. The latter mostly already did this.

* Examples for `confint.fderiv()` and `confint.gam()` were reworked, in part to
  remove some inconsistent output in the examples when run on M1 macs.

## Bug fixes

* `compare_smooths()` failed when passed non-standard model "names" like
  `compare_smooths(m_gam, m_gamm$gam)` or `compare_smooths(l[[1]], l[[2]])`
  even if the evaluated objects were valid GAM(M) models. Reported by Andrew
  Irwin #150

# gratia 0.7.2

## New features

* `draw.gam()` and `draw.smooth_estimates()` can now handle splines on the
  sphere (`s(lat, long, bs = "sos")`) with special plotting methods using
  `ggplot2::coord_map()` to handle the projection to spherical coordinates. An
  orthographic projection is used by default, with an essentially arbitrary
  (and northern hemisphere-centric) default for the orientation of the view.

* `fitted_values()` insures that `data` (and hence the returned object) is a
  tibble rather than a common or garden data frame.

## Bug fixes

* `draw.posterior_smooths()` was redundantly plotting duplicate data in the rug
  plot. Now only the unique set of covariate values are used for drawing the
  rug.

* `data_sim()` was not passing the `scale` argument in the bivariate example
  setting (`"eg2"`).

* `draw()` methods for `gamm()` and `gamm4::gamm4()` fits were not passing 
  arguments on to `draw.gam()`.

* `draw.smooth_estimates()` would produce a subtitle with data for a continuous
  by smooth as if it were a factor by smooth. Now the subtitle only contains the
  name of the continuous by variable.

# gratia 0.7.1

Due to an issue with the size of the package source tarball, which wasn't
discovered until after submission to CRAN, 0.7.1 was never released.

## New features

* `draw.gam()` and `draw.smooth_estimates()`: {gratia} can now handle smooths
  of 3 or 4 covariates when plotting. For smooths of 3 covariates, the third
  covariate is handled with `ggplot2::facet_wrap()` and a set (default `n` = 16)
  of small multiples is drawn, each a 2d surface evaluated at the specified
  value of the third covariate. For smooths of 4 covariates,
  `ggplot2::facet_grid()` is used to draw the small multiples, with the default
  producing 4 rows by 4 columns of plots at the specific values of the third
  and fourth covariates. The number of small multiples produced is controlled
  by new arguments `n_3d` (default = `n_3d = 16`) and `n_4d` (default
  `n_4d = 4`, yielding `n_4d * n_4d` = 16 facets) respectively.

  This only affects plotting; `smooth_estimates()` has been able to handle
  smooths of any number of covariates for a while.

  When handling higher-dimensional smooths, actually drawing the plots on the
  default device can be slow, especially with the default value of `n = 100`
  (which for 3D or 4D smooths would result in 160,000 data points being
  plotted). As such it is recommended that you reduce `n` to a smaller value:
  `n = 50` is a reasonable compromise of resolution and speed.

* `model_concurvity()` returns concurvity measures from `mgcv::concurvity()`
  for estimated GAMs in a tidy format. The synonym `concrvity()` is also
  provided. A `draw()` method is provided which produces a bar plot or a heatmap
  of the concurvity values depending on whether the overall concurvity of each
  smooth or the pairwise concurvity of each smooth in the model is requested.

* `draw.gam()` gains argument `resid_col = "steelblue3"` that allows the colour
  of the partial residuals (if plotted) to be changed.

## Bug fixes

* `model_edf()` was not using the `type` argument. As a result it only ever
   returned the default EDF type.

* `add_constant()` methods weren't applying the constant to all the required
  variables.

* `draw.gam()`, `draw.parametric_effects()` now actually work for a model with
  only parametric effects. #142 Reported by @Nelson-Gon

* `parametric_effects()` would fail for a model with only parametric terms
  because `predict.gam()` returns empty arrays when passed 
  `exclude = character(0)`.

# gratia 0.7.0

## Major changes

* `draw.gam()` now uses `smooth_estimates()` internally and consequently uses
  its `draw()` method and underlying plotting code. This has simplified the code
  compared to `evaluate_smooth()` and its methods, which will allow for future
  development and addition of features more easily than if `evaluate_smooth()`
  had been retained.

  Similarly, `evaluate_parametric_terms()` is now deprecated in favour of
  `parametric_effects()`, which is also used internally by `draw.gam()` if
  parametric terms are present in the model (and `parametric = TRUE`).

  While a lot of code has been reused so differences between plots as a result
  of this change should be minimal, some corner cases may have been missed. File
  an Issue if you notice something that has changed that you think shouldn't.

* `draw.gam()` now plots 2D isotropic smooths (TPRS and Duchon splines) with
  equally-scaled x and y coordinates using `coord_equal(ratio = 1)`. Alignment
  of these plots will be a little different now when plotting models with
  multiple smooths. See Issue #81.

### Deprecated functions

From version 0.7.0, the following functions are considered deprecated and their
use is discouraged:

* `fderiv()` is *soft*-deprecated in favour of `derivatives()`,
* `evaluate_smooth()` is *soft*-deprecated in favour of `smooth_estimates()`,
* `evaluate_parametric_term()` is *soft*-deprecated in favour of
  `parametric_effects()`.

The first call to one of these functions will generate a warning, pointing to
the newer, alternative, function. It is safe to ignore these warnings, but
these deprecated functions will no longer receive updates and are thus at risk
of being removed from the package at some future date. The newer alternatives
can handle more types of models and smooths, especially so in the case of
`smooth_estimates()`.

## New features

* `fitted_values()` provides a tidy wrapper around `predict.gam()` for
  generating fitted values from the model. New covariate values can be provided
  via argument `data`. A credible interval on the fitted values is returned, and
  values can be on the link (linear predictor) or response scale.

  Note that this function returns expected values of the response. Hence,
  "fitted values" is used instead of "predictions" in the case of new covariate
  values to differentiate these values from the case of generating new response
  values from a fitted model.

* `rootogram()` and its `draw()` method produce rootograms as diagnostic plots
  for fitted models. Currently only for models fitted with `poisson()`,
  `nb()`, `negbin()`, `gaussian()` families.

* New helper functions `typical_values()`, `factor_combos()` and
  `data_combos()` for quickly creating data sets for producing predictions from
  fitted models where some covariatess are fixed at come typical or
  representative values.

    `typical_values()` is a new helper function to return typical values for the
    covariates of a fitted model. It returns the value of the observation
    closest to the median for numerical covariates or the modal level of a
    factor while preserving the levels of that factor. `typical_values()` is
    useful in preparing data slices or scenarios for which fitted values from
    the estimated model are required.
    
    `factor_combos()` extracts and returns the combinations of levels of factors
    found in data used to fit a model. Unlike `typical_values()`,
    `factor_combos()` returns all the combinations of factor levels observed
    in the data, not just the modal level. Optionally, all combinations of
    factor levels can be returned, not just those in the observed data.
    
    `data_combos()` combines returns the factor data from `factor_combos()` plus
    the typical values of numerical covariates. This is useful if you want to
    generate predictions from the model for each combination of factor terms
    while holding any continuous covariates at their median values.

* `nb_theta()` is a new extractor function that returns the theta parameter of
  a fitted negative binomial GAM (families `nb()` or `negbin()`). Additionally,
  `theta()` and `has_theta()` provide additional functionality. `theta()` is an
  experimental function for extracting any additional parameters from the model
  or family. `has_theta()` is useful for checking if any additional parameters
  are available from the family or model. 

* `edf()` extracts the effective degrees of freedom (EDF) of a fitted model or a
  specific smooth in the model. Various forms for the EDF can be extracted.

* `model_edf()` returns the EDF of the overall model. If supplied with multiple
  models, the EDFs of each model are returned for comparison.

* `draw.gam()` can now show a "rug" plot on a bivariate smooth by drawing small
  points with high transparency over the smooth surface at the data coordinates.

  In addition, the rugs on plots of factor by smooths now show the locations of
  covariate values for the specific level of the factor and not over all levels.
  This better reflects what data were used to estimate the smooth, even though
  the basis for each smooth was set up using all of the covariate locations.

* `draw.gam()` and `draw.smooth_estimates()` now allow some aspects of the plot
  to be changed: the fill (but not colour) and alpha attributes of the credible
  interval, and the line colour for the smooth can now be specified using
  arguments `ci_col`, `ci_alpha`, and `smooth_col` respectively.

* Partial residuals can now be plotted on factor by smooths. To allow this, the
  partial residuals are filtered so that only residuals associated with a
  particular level's smooth are drawn on the plot of the smooth.

* `smooth_estimates()` uses `check_user_select_smooths()` to handle
  user-specified selection of smooth terms. As such it is more flexible than
  previously, and allows for easier selection of smooths to evaluate.

* `fixef()` is now imported (and re-exported) from the *nlme* package, with
  methods for models fitted with `gam()` and `gamm()`, to extract fixed effects
  estimates from fitted models. `fixed_effects()` is an alias for `fixef()`.

* The `draw()` method for `smooth_samples()` can now handle 2D smooths.
  Additionally, the number of posterior draws to plot can now be specified when
  plotting using new argument `n_samples`, which will result in `n_samples`
  draws being selected at random from the set of draws for plotting. New
  argument `seed` allows the selection of draws to be repeatable.

## Bug fixes

* `smooth_estimates()` was not filtering user-supplied data for the by level of
  the specific smooth when used with by factor smooths. This would result in the
  smooth being evaluated at all rows of the user-supplied data, and therefore
  would result in `nrow(user_data) * nlevels(by_variable)` rows in the returned
  object instead of `nrow(user_data)` rows.

* The `add_confint()` method for `smooth_estimates()` had the upper and lower
  intervals reversed. #107 Reported by @Aariq

* `draw.gam()` and `smooth_estimates()` were both ignoring the `dist` argument
  that allows covariate values that lie too far from the support of the data to
  be excluded when returning estimated values from the smooth and plotting it.
  #111 Reported by @Aariq

* `smooth_samples()` with a factor by GAM would return samples for the first
  factor level only. Reported by @rroyaute in discussion of #121

* `smooth_samples()` would fail if the model contained random effect "smooths".
  These are now ignored with a message when running `smooth_samples()`.
  Reported by @isabellaghement in #121

* `link()`, `inv_link()` were failing on models fitted with `family = scat()`.
  Reported by @Aariq #130

# gratia 0.6.0

## Major changes

* The {cowplot} package has been replaced by the {patchwork} package for
  producing multi-panel figures in `draw()` and `appraise()`. This shouldn't
  affect any code that used {gratia} only, but if you passed additional
  arguments to `cowplot::plot_grid()` or used the `align` or `axis` arguments of
  `draw()` and `appraise()`, you'll need to adapt code accordingly.

  Typically, you can simply delete the `align` or `axis` arguments and
  {patchwork} will just work and align plots nicely. Any arguments passed via
  `...` to `cowplot::plot_grid()` will just be ignored by
  `patchwork::wrap_plots()` unless those passed arguments match any of the
  arguments of `patchwork::wrap_plots()`.

## New features

* The {patchwork} package is now used for multi-panel figures. As such, {gratia}
  no longer Imports from the {cowplot} package.

* Worm plot diagnostic plots are available via new function `worm_plot()`. Worm
  plots are detrended Q-Q plots, where deviation from the Q-Q reference line are
  emphasized as deviations around the line occupy the full height of the plot.

  `worm_plot()` methods are available for models of classes `"gam"`, `"glm"`,
  and `"lm"`. (#62)

* Smooths can now be compared across models using `compare_smooths()`, and
  comparisons visualised with the associated `draw()` method. (#85 @dill)

  This feature is a bit experimental; the returned object uses nested lists and
  may change in the future if users find this confusing.

* The reference line in `qq_plot()` with `method = "normal"` was previously
  drawn as a line with intercept 0 and slope 1, to match the other methods. This
  was inconsistent with `stats::qqplot()` which drew the line through the 1st
  and 3rd quartiles. `qq_plot()` with `method = "normal"` now uses this robust
  reference line. Reference lines for the other methods remain drawn with slope
  1 and intercept 0.

* `qq_plot()` with `method = "normal"` now draws a point-wise reference band
  using the standard error of the order statistic.

* The `draw()` method for `penalty()` now plots the penalty matrix heatmaps in a
  more-logical orientation, to match how the matrices might be written down or
  printed to the R console.

* `link()`, and `inv_link()` now work for models fitted with the `gumbls()` and
  `shash()` families. (#84)

* `extract_link()` is a lower level utility function related to `link()` and
  `inv_link()`, and is now exported.

## User visible changes

* The default method name for generating reference quantiles in `qq_plot()` was
  changed from `"direct"` to `"uniform"`, to avoid confusion with the
  `mgcv::qq.gam()` help page description of the methods. Accordingly using
  `method = "direct"` is deprecated and a message to this effect is displayed if
  used.

* The way smooths/terms are selected in `derivatives()` has been switched to use
  the same mechanism as `draw.gam()`'s `select` argument. To get a partial match
  to `term`, you now need to also specify `partial_match = TRUE` in the call to
  `derivatives()`.

## Bug fixes

* `transform_fun()` had a copy paste bug in the definition of the then generic.
  (#96 @Aariq)

* `derivatives()` with user-supplied `newdata` would fail for factor by smooths
  with `interval = "simultaneous"` and would introduce rows with derivative == 0
  with `interval = "confidence"` because it didn't subset the rows of `newdata`
  for the specific level of the by factor when computing derivatives.
  (#102 @sambweber)

* `evaluate_smooth()` can now handle random effect smooths defined using an
  ordered factor. (#99 @StefanoMezzini)

# gratia 0.5.1

## New features

* `smooth_estimates()` can now handle

    * bivariate and multivariate thinplate regression spline smooths, e.g. 
      `s(x, z, a)`,
    * tensor product smooths (`te()`, `t2()`, & `ti()`), e.g. `te(x, z, a)`
    * factor smooth interactions, e.g. `s(x, f, bs = "fs")`
    * random effect smooths, e.g. `s(f, bs = "re")`

* `penalty()` provides a tidy representation of the penalty matrices of
  smooths. The tidy representation is most suitable for plotting with
  `ggplot()`.

  A `draw()` method is provided, which represents the penalty matrix as a
  heatmap.

## User visible changes

* The `newdata` argument to `smooth_estimates()` has been changed to `data` as
  was originally intended.

# gratia 0.5.0

## New features

* Partial residuals for models can be computed with `partial_residuals()`. The
  partial residuals are the weighted residuals of the model added to the
  contribution of each smooth term (as returned by `predict(model,
  type = "terms")`.
  
  Wish of [#76](https://github.com/gavinsimpson/gratia/issues/76) (@noamross)

  Also, new function `add_partial_residuals()` can be used to add the partial
  residuals to data frames.

* Users can now control to some extent what colour or fill scales are used when
  plotting smooths in those `draw()` methods that use them. This is most useful
  to change the fill scale when plotting 2D smooths, or to change the discrete
  colour scale used when plotting random factor smooths (`bs = "fs"`).
  
  The user can pass scales via arguments `discrete_colour` and
  `continuous_fill`.

* The effects of certain smooths can be excluded from data simulated from a
  model using `simulate.gam()` and `predicted_samples()` by passing `exclude` or
  `terms` on to `predict.gam()`. This allows for excluding random effects, for
  example, from model predicted values that are then used to simulate new data
  from the conditional distribution. See the example in `predicted_samples()`.
  
  Wish of [#74](https://github.com/gavinsimpson/gratia/issues/74) (@hgoldspiel)

* `draw.gam()` and related functions gain arguments `constant` and `fun` to
  allow for user-defined constants and transformations of smooth estimates and
  confidence intervals to be applied.
  
  Part of wish of Wish of
  [#79](https://github.com/gavinsimpson/gratia/issues/79).

* `confint.gam()` now works for 2D smooths also.

* `smooth_estimates()` is an early version of code to replace (or more likely
  supersede) `evaluate_smooth()`. `smooth_estimates()` can currently only handle
  1D smooths of the standard types. 

## User visible changes

* The meaning of `parm` in `confint.gam` has changed. This argument now requires
  a smooth label to match a smooth. A vector of labels can be provided, but
  partial matching against a smooth label only works with a single `parm` value.
  
  The default behaviour remains unchanged however; if `parm` is `NULL` then all
  smooths are evaluated and returned with confidence intervals.

* `data_class()` is no longer exported; it was only ever intended to be an
  internal function.

## Bug Fixes

* `confint.gam()` was failing on a tensor product smooth due to matching issues.
  Reported by @tamas-ferenci
  [#88](https://github.com/gavinsimpson/gratia/issues/88)
  
  This also fixes [#80](https://github.com/gavinsimpson/gratia/issues/80)
  (@butellyn) which was a related issue with selecting a specific smooth.

* The **vdiffr** package is now used conditionally in package tests.
  Reported by Brian Ripley
  [#93](https://github.com/gavinsimpson/gratia/issues/93)

# gratia 0.4.1

## User visible changes

* `draw.gam()` with `scales = "fixed"` now applies to all terms that can be
  plotted, including 2d smooths.

  Reported by @StefanoMezzini
  [#73](https://github.com/gavinsimpson/gratia/issues/73)

## Bug fixes

* `dplyr::combine()` was deprecated. Switch to `vctrs::vec_c()`.

* `draw.gam()` with `scales = "fixed"` wasn't using fixed scales where 2d
  smooths were in the model.

  Reported by @StefanoMezzini
  [#73](https://github.com/gavinsimpson/gratia/issues/73)

# gratia 0.4.0

## New features

* `draw.gam()` can include partial residuals when drawing univariate smooths.
  Use `residuals = TRUE` to add partial residuals to each univariate smooth that
  is drawn. This feature is not available for smooths of more than one variable,
  by smooths, or factor-smooth interactions (`bs = "fs"`).

* The coverage of credible and confidence intervals drawn by `draw.gam()` can be
  specified via argument `ci_level`. The default is arbitrarily `0.95` for no
  other reason than (rough) compatibility with `plot.gam()`.
  
  This change has had the effect of making the intervals slightly narrower than
  in previous versions of *gratia*; intervals were drawn at &plusmn; 2 &times;
  the standard error. The default intervals are now drawn at &plusmn; ~1.96
  &times; the standard error.

* New function `difference_smooths()` for computing differences between factor
  smooth interactions. Methods available for `gam()`, `bam()`, `gamm()` and
  `gamm4::gamm4()`. Also has a `draw()` method, which can handle differences of
  1D and 2D smooths currently (handling 3D and 4D smooths is planned).

* New functions `add_fitted()` and `add_residuals()` to add fitted values
  (expectations) and model residuals to an existing data frame. Currently
  methods available for objects fitted by `gam()` and `bam()`.

* `data_sim()` is a tidy reimplementation of `mgcv::gamSim()` with the added
  ability to use sampling distributions other than the Gaussian for all models
  implemented. Currently Gaussian, Poisson, and Bernoulli sampling distributions
  are available.

* `smooth_samples()` can handle continuous by variable smooths such as in
  varying coefficient models.

* `link()` and `inv_link()` now work for all families available in *mgcv*,
  including the location, scale, shape families, and the more specialised
  families described in `?mgcv::family.mgcv`.

* `evaluate_smooth()`, `data_slice()`, `family()`, `link()`, `inv_link()`
  methods for models fitted using `gamm4()` from the **gamm4** package.

* `data_slice()` can generate data for a 1-d slice (a single variable varying).

* The colour of the points, reference lines, and simulation band in `appraise()`
  can now be specified via arguments

    * `point_col`,
    * `point_alpha`,
    * `ci_col`
    * `ci_alpha`
    * `line_col`

  These are passed on to `qq_plot()`, `observed_fitted_plot()`,
  `residuals_linpred_plot()`, and `residuals_hist_plot()`, which also now take
  the new arguments were applicable.

* Added utility functions `is_factor_term()` and `term_variables()` for working
  with models. `is_factor_term()` identifies is the named term is a factor using
  information from the `terms()` object of the fitted model. `term_variables()`
  returns a character vector of variable names that are involved in a model
  term. These are strictly for working with parametric terms in models.

* `appraise()` now works for models fitted by `glm()` and `lm()`, as do the
  underlying functions it calls, especially `qq_plot`.
  
  `appraise()` also works for models fitted with family `gaulss()`. Further
  location scale models and models fitted with extended family functions will
  be supported in upcoming releases.

## User visible changes

* `datagen()` is now an *internal* function and is no longer exported. Use
  `data_slice()` instead.

* `evaluate_parametric_term()` is now much stricter and can only evaluate main
  effect terms, i.e. those whose order, as stored in the `terms` object of the
  model is `1`.

## Bug fixes

* The `draw()` method for `derivatives()` was not getting the x-axis label for
  factor by smooths correctly, and instead was using `NA` for the second and
  subsequent levels of the factor.

* The `datagen()` method for class `"gam"` couldn't possibly have worked for
  anything but the simplest models and would fail even with simple factor by
  smooths. These issues have been fixed, but the behaviour of `datagen()` has
  changed, and the function is now not intended for use by users.

* Fixed an issue where in models terms of the form `factor1:factor2` were
  incorrectly identified as being numeric parametric terms.
  [#68](https://github.com/gavinsimpson/gratia/issues/68)

# gratia 0.3.1

## New features

* New functions `link()` and `inv_link()` to access the link function and its
  inverse from fitted models and family functions.
  
  Methods for classes: `"glm"`, `"gam"`, `"bam"`, `"gamm"` currently.
  [#58](https://github.com/gavinsimpson/gratia/issues/58)

* Adds explicit `family()` methods for objects of classes `"gam"`, `"bam"`, and
  `"gamm"`.

* `derivatives()` now handles non-numeric when creating shifted data for finite
  differences. Fixes a problem with `stringsAsFactors = FALSE` default in
  R-devel. [#64](https://github.com/gavinsimpson/gratia/issues/64)

## Bug fixes

* Updated *gratia* to work with *tibble* versions >= 3.0

# gratia 0.3.0

## New features

* *gratia* now uses the *mvnfast* package for random draws from a multivariate
  normal distribution (`mvnfast::rmvn()`). Contributed by Henrik Singmann
  (@singmann) [#28](https://github.com/gavinsimpson/gratia/issues/28)

* New function `basis()` for generating tidy representations of basis expansions
  from an *mgcv*-like definition of a smooth, e.g. `s()`, `te()`, `ti()`, or
  `t2()`. The basic smooth types also have a simple `draw()` method for plotting
  the basis. `basis()` is a simple wrapper around `mgcv::smoothCon()` with some
  post processing of the basis model matrix into a tidy format.
  [#42](https://github.com/gavinsimpson/gratia/issues/42)

* New function `smooth_samples()` to draw samples of entire smooth functions
  from their posterior distribution. Also has a `draw()` method for plotting the
  posterior samples.

## Bug fixes

* `draw.gam()` would produce empty plots between the panels for the parametric
    terms if there were 2 or more parametric terms in a model. Reported by
    @sklayn [#39](https://github.com/gavinsimpson/gratia/issues/39).

* `derivatives()` now works with factor by smooths, including ordered factor by
    smooths. The function also now works correctly for complex models with
    multiple covariates/smooths.
    [#47](https://github.com/gavinsimpson/gratia/issues/47)

    `derivatives()` also now handles `'fs'` smooths.  Reported by
    @tomand-uio [#57](https://github.com/gavinsimpson/gratia/issues/57).

* `evaluate_parametric_term()` and hence `draw.gam()` would fail on a `ziplss()`
  model because i) *gratia* didn't handle parametric terms in models with
  multiple linear predictors correctly, and ii) *gratia* didn't convert to the
  naming convention of *mgcv* for terms in higher linear predictors. Reported by
  @pboesu [#45](https://github.com/gavinsimpson/gratia/issues/45)
