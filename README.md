# covid19-projections.com SEIR Simulator

We present the underlying SEIR model simulator behind [covid19-projections.com](https://covid19-projections.com), as well a summarized set of parameters that helped generate the projections. You can generate your own simulations in under 5 minutes.

## Table of Contents
* [Introduction](#introduction)
* [Dependencies](#dependencies)
* [Setup](#setup)
* [Usage](#usage)
  * [Basic Usage](#basic-usage)
  * [Advanced Usage](#advanced-usage)
  * [Setting Parameters](#setting-parameters)
  * [Changing Parameters](#changing-parameters)
  * [Using Your Own Parameters](#using-your-own-parameters)
* [Parameters](#parameters)
* [Questions?](#question-bug-feedback-suggestion)

## Introduction

To begin, we want to be clear that this is **not** the full model used by [covid19-projections.com](https://covid19-projections.com). This is the underlying SEIR model without the machine learning layer to learn the parameters. In fact, this simulator does not use any real data - it only simulates infections, hospitalizations, and deaths given a single set of parameters.

Because this simulator does not use real data and has very little [dependencies](#dependencies), it is very easy to run and works right out of the box. We've purposedly designed our simulator to be as lean and simple as possible.

The simulations produced by this program will not necessarily match the full model, but it is often be a close approximation. The full model for *covid19-projections.com* generates thousands of parameter sets for each region and weighs the parameters based on how the resulting simulations match the observed data. With that said, this is the full, unabridged SEIR model used to generate the simulations - no modifications have been made for this release.

In addition to the SEIR simulator, we provide the "best" set of parameters that our machine learning layer has learned to best fit the real-world observed data. This is done by taking a weighted mean (or median) of the individual parameters used in our full model. Because parameters are not independent, using only a single set of parameters may skew the simulation results when compared to the full model projections.

While this simulator may not be best suited to make projections (since it does not use real data), we believe this simulator is particular helpful for mapping out scenarios. For example, what would happen if [people started social distancing 7 days earlier](#decrease-inflection-date-by-7-days). Or what if [20% of individuals self-quarantine after symptom onset](#simulate-effect-of-quarantine). Or if there was [no reopening](#assume-no-reopening).

## Dependencies

You need [Python 3](https://www.python.org/downloads/) and [NumPy](https://numpy.org/install/). That's it.

*Note: This was built and tested on Python 3.8, so it's possible that an older Python 3.x version may not be compatible.*

## Setup

1. Make sure you have [Python 3](https://www.python.org/downloads/) and [NumPy](https://numpy.org/install/) installed.
2. Clone our repository: ```git clone https://github.com/youyanggu/yyg-c19pro-model-public.git```
3. You are now ready to run our simulator! See sample usage below.

## Usage

To view a list of supported countries, states, and subregions, check [covid19-projections.com](https://covid19-projections.com/#view-projections).

#### List all command line flags/features:
```
python run_simulation.py --help
```

### Basic Usage

Note: `-v` means verbose mode. Remove the flag and you will get fewer print statements.

#### US simulation:
```
python run_simulation.py -v --best_params_dir best_params/latest --country US
```

#### US state simulations:
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --region NY
python run_simulation.py -v --best_params_dir best_params/latest --country US --region AZ
python run_simulation.py -v --best_params_dir best_params/latest --country US --region TX
```

#### US county simulations:
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --region CA --subregion "Los Angeles"
python run_simulation.py -v --best_params_dir best_params/latest --country US --region NY --subregion "New York City"
python run_simulation.py -v --best_params_dir best_params/latest --country US --region FL --subregion Miami-Dade
```

#### Global country simulations:
```
python run_simulation.py -v --best_params_dir best_params/latest --country Brazil
python run_simulation.py -v --best_params_dir best_params/latest --country Sweden
python run_simulation.py -v --best_params_dir best_params/latest --country Iran
```

#### Canadian province simulation:
```
python run_simulation.py -v --best_params_dir best_params/latest --country Canada --subregion Quebec
python run_simulation.py -v --best_params_dir best_params/latest --country Canada --subregion "British Columbia"
```

### Advanced Usage

#### Save outputs to CSV file
```
python run_simulation.py -v --best_params_dir best_params/latest --country US  --save_csv_fname us_simulation.csv
```

#### Show hospitalizations
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --compute_hospitalizations
```

#### Simulate effect of quarantine
In this scenario, we show how the course of the epidemic would be different if just 20% of infected individuals immediately self-quarantine after showing symptoms, reducing their own transmission by 25%. For the remaining 80% of infected individuals, we assume normal transmission. You can see a graph of this scenario [here](https://covid19-projections.com/us-self-quarantine).
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --quarantine_perc 0.2 --quarantine_effectiveness 0.25
```

#### Use an older set of best parameters
By default, we use the latest set of best parameters in `best_params/latest`, but you can also specify a different directory.
```
python run_simulation.py -v --best_params_dir best_params/2020-06-20 --country US
```

#### Use the top 10 best parameters rather than the mean
The four choices are: mean (default), median, top, top10
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --best_params_type top10
```

### Setting Parameters

We also provide support to customize your own parameters using the `--set_param` flag. The flag takes a pair of values: the name of the parameter to be changed and its value. You can use the flag multiple times.

See the available parameter options in the [next section](#parameters).

#### Set initial R_0 to 2.0
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --set_param INITIAL_R_0 2.0
```

#### Set initial R_0 to 2.0 and lockdown R_t to 0.9
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --set_param INITIAL_R_0 2.0 --set_param LOCKDOWN_R_0 0.9
```

#### Set the reopen date to June 1, 2020
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --set_param REOPEN_DATE 2020-06-01
```

#### Assume no reopening
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --set_param REOPEN_R_MULT 1
```

### Changing parameters

Sometimes you don't want to replace an existing parameter, but rather just change it. The `--change_param` flag takes a pair of values: the name of the parameter to be changed and the amount to change the value by. You can use the flag multiple times.

See the available parameter options in the [next section](#parameters).

#### Increase initial R_0 by 0.1
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --change_param INITIAL_R_0 0.1
```

#### Increase initial R_0 by 0.1 and decrease lockdown R_t by 0.1
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --change_param INITIAL_R_0 0.1 --change_param LOCKDOWN_R_0 -0.1
```

#### Increase reopening date by 7 days
This simulates what would happen if people in the US reopened one week later.
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --change_param REOPEN_DATE 7
```

#### Decrease inflection date by 7 days
This simulates what would happen if people in the US started social distancing one week earlier. You can see a graph of the results [here](https://covid19-projections.com/us-1weekearlier).
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --change_param INFLECTION_DAY -7
```

#### Set initial_R_0 to 2.2
You can also set the R_0 using `---set_param`, and then change it via `--change_param`. See below for an alternate way to set the initial R_0 to 2.2:
```
python run_simulation.py -v --best_params_dir best_params/latest --country US --set_param INITIAL_R_0 2.0 --change_param INITIAL_R_0 0.2
```

### Using Your Own Parameters
Don't want to use our parameter set? Want to run a simulation for a region/country we do not support? No problem! You can simply modify `run_simulations.py` (in the `main()` function) and specify your own default parameters (list of parameters [below](#parameters)). Afterwards, you can simply run:
```
python run_simulation.py -v
```

Note: You can still use the `--set_param` and `--change_param` flags from above, as well as the other flags explained in [Advanced Usage](#advanced-usage).

## Parameters

There are several parameters that we need to provide the simulator before it can run. We describe them below:

#### `INITIAL_R_0`

This is the initial basic reproduction number (R_0). Read more about our R_t estimates [here](https://covid19-projections.com/about/#effective-reproduction-number-r).

#### `LOCKDOWN_R_0`

This is the post-mitigation effective reproduction number (R_t).

#### `INFLECTION_DAY`

This is the date of the inflection point when `INITIAL_R_0` transitions to `LOCKDOWN_R_0`. This is usually an indicator of when people in a region began social distancing. For example, in New York, the inflection day is around [March 14](https://twitter.com/youyanggu/status/1262881629376180227).

#### `RATE_OF_INFLECTION`

This is the rate at which the `INITIAL_R_0` transitions to `LOCKDOWN_R_0`. A number closer to 1 indicates a faster transition (i.e. faster lockdown), while a number closer to 0 indicates a slower transition. For most region, this value is between 0.15 and 0.5. The more localized a region, the higher the rate of inflection. So for example, we estimate New York City has a `RATE_OF_INFLECTION` between 0.4-0.5, while the US as an entire country has a `RATE_OF_INFLECTION` of approximately 0.2-0.3.

#### `LOCKDOWN_FATIGUE`

We incorporate a lockdown fatigue multiplier that is applied to the R_t. This is to simulate the increase in mobility after several weeks of lockdown, despite the order not being lifted. If this value is greater than 1, then it will contribute to an increase in infections in the weeks following the lockdown/mitigation. We do not use this for the majority of our projections.

#### `DAILY_IMPORTS`

To begin our simulation, we must initialize / bootstrap our model with an initial number of infected individuals. Any epidemic in a region must begin with a number of imported individuals. This value gradually goes to 0 as community spread overtakes imported cases as the major source of spread.

#### `MORTALITY_RATE`

This is the initial estimate of the infection fatality rate (IFR). Read more about our IFR estimates [here](https://covid19-projections.com/about/#infection-fatality-rate-ifr).

#### `REOPEN_DATE`

This is the date we estimate the region to reopen. Read more about our reopening assumptions [here](https://covid19-projections.com/about/#social-distancing).

#### `REOPEN_SHIFT_DAYS`

Even though some regions open on the same date, infections can begin increasing faster or slower than we'd normally expect. We use the `REOPEN_SHIFT_DAYS` to account for that. For example, a value of 7 means that we are shifting the `REOPEN_DATE` to be 7 days later, while a value of -7 means we are shifting it to be 7 days earlier.

#### `REOPEN_R_MULT`

This is the multiplier we apply to the `LOCKDOWN_R_0` to generate the reopening R_t value. We assume this value must be greater or equal to 1. For a typical region, we expect the R_t to increase by around 0-30%, so this multiplier is 1-1.3.

#### `POST_REOPENING_R_DECAY`

This is the multiplier we apply to the R_t approximately 15-30 days after reopening. After some time has passed following a reopening, we assume that the spread will decrease over time due to improvements in contact testing and greater awareness within the population. Read more about our post-reopening assumptions [here](https://covid19-projections.com/about/#post-reopening).

## Question? Bug? Feedback? Suggestions?

We welcome questions, bug reports, feedback and suggestions. You can find a lot of information on [covid19-projections.com](https://covid19-projections.com/about/). But feel free to open an [issue ticket](https://github.com/youyanggu/yyg-c19pro-model-public/issues) if your question was not answered.