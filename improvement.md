# IPL Auction Price Prediction: Improvement Roadmap

Created: 29 September 2026

## Purpose and scope

Revisit this project ahead of the next IPL auction. Improve the methodology,
compare against simple baselines, and publish predictions before auction results
are known. This is a future-work checklist, not a record of completed improvements.

No changes to the existing notebook, CV, or CricAtlas are part of this roadmap.
Do not wait for the auction itself to freeze predictions: preparation must finish
before bidding begins. Verify the official schedule when resuming this work.

## 1. Audit the existing experiment

- [ ] Separate synthetic-data demonstrations from real-player experiments.
- [ ] Verify player identities, auction years, price sources and currency units.
- [ ] Distinguish auction purchase prices from retention prices and base prices.
- [ ] Check that each player's performance data predates the auction being predicted.
- [ ] Record the actual input features used by each experiment; do not describe
      proposed features as already implemented.
- [ ] Recompute the current results from a clean run and preserve them as the
      original experiment, clearly labelled as training or held-out results.

### Findings to verify when resuming

In the previously inspected 30-player experiment, the scaler and linear SVR were
fitted on the full dataset, and predictions were evaluated on that same dataset.
The model used Matches, Innings, Runs and BallsFaced. Its approximately 3.72 MAE
was in crore INR and was a training-set result, not unseen predictive accuracy.

A post-hoc specialist-batter subset retained David Miller and produced an MAE
of approximately 2.47 crore INR over 11 players. It did not involve retraining.
That subset's own median-price constant produced approximately 2.27 MAE, but
this too was an in-sample descriptive comparison, not a valid future baseline.
Including Nitish Rana as a batter changed the subset MAE to approximately 2.28.

These observations motivate better validation. They are not evidence that role
features have already improved the model, and the smaller subset score must not
replace the full-sample score without stating the changed population.

## 2. Define the prediction task before modelling

- [ ] Decide whether the scope covers all registered players, all auctioned
      players, or a clearly defined role group.
- [ ] Define how unsold, withdrawn and unavailable players are handled. An unsold
      player has no observed sale price; do not silently treat that price as zero.
- [ ] Consider two tasks if data permits: probability of being sold, and sale price
      conditional on being sold. Report their results separately.
- [ ] Define player-role categories in advance using a documented source; record
      ambiguous cases consistently rather than choosing classifications by error.
- [ ] Keep David Miller and other difficult examples whenever they meet the
      pre-defined inclusion criteria. Do not remove players because predictions
      are poor.

## 3. Build a historically valid dataset

- [ ] Collect multiple auction years if feasible; distinguish mini and mega auctions.
- [ ] Save source URLs, collection dates, units and a data dictionary.
- [ ] Use stable player IDs and check duplicate records, missing values and joins.
- [ ] Create features using only information available before each auction.
- [ ] Include candidate features such as batting/bowling performance, recent form,
      age, base price, capped status, overseas status and availability where reliable.
- [ ] Test role indicators for specialist batters, wicketkeeper-batters, all-rounders
      and bowlers. Document overlaps and the classification policy.
- [ ] Treat captaincy experience as a testable, objectively sourced feature rather
      than retrospectively assigning a subjective "captaincy premium".
- [ ] Consider purse levels, roster needs and auction type if enough data exists.
      Do not use information revealed after the prediction cutoff.

## 4. Establish baselines and chronological validation

- [ ] Train on earlier auctions and validate on a later auction. If enough seasons
      exist, repeat this as rolling chronological backtests.
- [ ] Fit scaling, imputation and feature selection on training data only, ideally
      inside a single reproducible modelling pipeline.
- [ ] Calculate a global median-price baseline from training data only.
- [ ] Calculate role-specific median baselines from training data only, with a
      documented fallback for small or unseen groups.
- [ ] Compare those baselines with a simple regularised linear model and the SVR.
      Add a more complex model only if it improves held-out performance.
- [ ] Tune on historical validation data, not on the final held-out auction.
- [ ] Compare identical splits with and without role features to test whether they
      actually improve predictions.

## 5. Report meaningful results

- [ ] Report MAE with its currency unit and the evaluated player count.
- [ ] Include median absolute error and a measure sensitive to large misses, such
      as RMSE, to make the error distribution visible.
- [ ] Show error by pre-defined role, price band and auction year where sample
      sizes support meaningful comparisons.
- [ ] Report subgroup sample sizes and avoid strong conclusions from tiny groups.
- [ ] Plot actual versus predicted price and examine systematic over/underprediction.
- [ ] Inspect failures such as David Miller rather than hiding them.
- [ ] Distinguish observed associations from explanations: a role-related error
      pattern does not establish why franchises paid a particular price.
- [ ] Explain limitations, including small samples, bidding dynamics, changing
      auction conditions and information unavailable to the model.

## 6. Run a genuinely prospective auction test

- [ ] Verify the official auction schedule and player list when work resumes.
- [ ] Select a prediction cutoff before bidding begins.
- [ ] Freeze the dataset, features, code version, model and evaluation rules.
- [ ] Commit a timestamped predictions file before the auction, including player ID,
      role, predicted price, units and model version. Save baseline predictions too.
- [ ] Do not overwrite this original file after outcomes become known.
- [ ] After the auction, join official outcomes and evaluate using the frozen rules.
- [ ] Account explicitly for unsold players and any withdrawals or missing results.
- [ ] Publish the complete results, baseline comparison, role breakdowns and lessons.
      Label any later model updates as a new experiment.

## Definition of a stronger project

The main deliverable is not necessarily a lower headline MAE. It is a reproducible,
leakage-aware experiment showing whether the model beats simple baselines on unseen
auctions, where it succeeds or fails, and what decisions its results can support.

Suggested future outputs: a cleaned data dictionary, chronological evaluation
notebook, baseline comparison, frozen pre-auction predictions and post-auction report.
