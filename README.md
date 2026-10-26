<img src="notebooks/img/utexas_ds_orie_header.png" alt="drawing" style="width:900px;"/>
<img src="notebooks/img/utexas_ds_orie_divider.png" alt="drawing" style="width:900px;"/>

This University of Texas course teaches Data Science through Experiential Learning.  Students work with data sets from day one and progressively gain Data Science skills as they learn from their mistakes.

**This is the Mechanical Engineering flavor of the course.** Every notebook is built around problems a mechanical engineer would recognize: 3D-printed part strength, jet engine emissions, weld inspection, and an auto parts business. The general version of the course lives at [iyoob-utexas/ds4e](https://github.com/iyoob-utexas/ds4e).

## About the course

This course provides students with the knowledge to analyze and interpret data sets using industry-standard methodology. Key skills include learning how to clean, visualize, and perform industry-specific analysis on large data sets.  Using predictive analysis and machine learning, students will build models to make defensible connections in the data. Upon finishing this model, students will verify/validate their hypothesis and conclusions. The course culminates in the creation of an interactive dashboard presenting the given correlations. By “telling the story” of their journey through the data, students will relay the skills they learned to their peers.

## About the instructor

Dr. Ilyas Iyoob is faculty of Data Science and Artificial Intelligence in the Cockrell School of Engineering at the University of Texas.  He pioneered the seamless interaction between machine learning and operations research in the fields of autonomous computing, health-tech, and fin-tech.  Previously, Dr. Iyoob helped build a cloud computing AI startup for 9 years and successfully sold it to IBM in 2016.  He currently advises over a dozen venture funded companies and serves as the Global Head of Research at Kyndryl (IBM Spinoff).  He has earned a number of patents and industry recognition for applied Artificial Intelligence and was awarded the prestigious World Mechanics prize by the University of London.

## Datasets

Data Science for Engineers (ME 365D). All data is synthetic, generated from scratch with fixed seeds, shaped after real engineering sources. ME 335 teaches what the models are, conceptually; this elective is where students build them.

| # | File | Rows × cols | Students answer | Inspired by |
|---|---|---|---|---|
| n1 | `prints.csv` | 244 × 7 | How do print settings change a printed bar's tensile strength? | FDM tensile-test studies (Kaggle 3D printer dataset) |
| n2, n3 | `engine_tests.csv` | 3,500 × 23 | How much NOx does a jet engine emit per landing-takeoff cycle? | ICAO Aircraft Engine Emissions Databank (EASA v32, 03/2026) |
| n4 | `weld_inspections.csv` | 303 × 14 | Given several inspection results, is this weld cracked? | NDT probability-of-detection practice (MIL-HDBK-1823A) |
| n5 | MNIST, as handwritten gauge readings | | | |
| n6, n7 | `parts_orders.csv` | 200,781 × 8 | What kinds of buyers are there? What parts go together in a repair job? | Amazon Reviews 2023 (Automotive) |
| n8 | `parts_catalog.csv` + `parts_ratings.csv` | 1,099 × 24 + 39,324 × 4 | Which parts are similar? Which parts will this buyer rate highly? | Amazon Reviews 2023 (Automotive) |
| n9, n10 | Shipping and transformers data | | | |

Join keys across the parts files: `orders.StockCode = catalog.stock_code`, `ratings.partId = catalog.partId`, `ratings.userId = orders.CustomerID`. 100% of rated parts are in the catalog and 100% of raters placed orders.

## What each dataset is built to teach (verified 2026-10-19)

Full output: `verification_output.txt`. Rerun with `generators/verify_datasets.py`.

| # | Planted structure | Result on the generated data |
|---|---|---|
| n1 | Strength ≈ linear in infill; PLA stronger than ABS; walls help; printer Delta mis-calibrated; shift has no real effect; printer Bravo's tester rounds to whole MPa | Linear R² 0.68 · PLA slope 0.42 vs ABS 0.30 MPa per % · Bravo 100% whole numbers vs 5% elsewhere · Day vs Night means 27.3 vs 25.6 (chance imbalance, no effect in the generator) |
| n2 | NOx grows with thrust and pressure ratio | Full tree in-sample MAE 0.00, test 1.62 kg · best at 500 leaves (1.52) · random forest 1.14 · depth-2 tree splits on thrust |
| n3 | ~46% missing dry mass, ~39% missing certification year, lbf-for-kN typos, combustor type that varies within an engine family | Drop 1.163 → ffill 1.157 → mean impute 1.140 → **+ categories 0.678** → boosting 0.509 kg · Lean-burn cuts NOx most yet its raw average (7.0) beats Low-emission (6.2): confounded by engine size |
| n4 | ~8% crack rate; inspection methods independent given crack | 23 cracks · AUC: Naive Bayes 0.97, logistic 0.99, SVM 0.98 · P(crack \| strong ultrasonic) 0.59 → 1.00 once X-ray also shows a linear mark |
| n6 | Five buyer types (DIY, repair shop, fleet, enthusiast, detailer) | k = 3 on invoice count × spend gives DIY (4,906, median $254), shops (355, $22K), fleets (134, $32K) · silhouette 0.89 |
| n7 | Repair-job bundles, ~2% "C" returns, 23% walk-in sales with no customer ID | 91 rules at support ≥ 1%, confidence ≥ 0.7, from brake jobs (26), oil changes (31), detailing (31), cooling (2) |
| n8 | Tier taste, performance and detailing fans, rater generosity, a few badly fitting parts | Content-based on Camry front pads returns other front pads and the Camry rear pads · SVD RMSE 0.65 vs 0.85 for predicting the average |

## Notebooks (in `notebooks/`)

All 12 notebooks: c, n1-n10 and samples. Outputs are cleared. Each was executed end to end locally on 2026-10-19; the only error is n3's deliberate "all features" failure.

| # | Notes |
|---|---|
| n1 | Option 1 says which charts to build in Power BI. Narrative covers whole-MPa rounding on printer Bravo, delaminated bars, PLA vs ABS slopes |
| n2 | Narrative numbers: in-sample 0 kg, split 1.67 kg (23% of 7.2 kg mean), 500 leaves 21%, random forest 1.14 kg (16%). Notes the lbf-for-kN outliers in `describe()` |
| n3 | All imputation methods are compared on the same random split (random_state 1). Notes that scikit-learn ≥ 1.4 forests accept NaN. XGBoost wins even at default settings (0.58 vs 0.68 kg). Includes the lean-burn confounding note |
| n4 | Split uses `stratify=y` (rare class). Cells after Naive Bayes on base rates: P(crack) 0.08 → 0.59 with a strong echo → 1.00 with a linear X-ray too. Naive Bayes is best (AUC 0.87); KNN scores 0.50 because it never predicts a crack |
| n6 | DBSCAN eps loop stops at 1; larger eps puts every customer in one cluster and `silhouette_score` errors. Narrative: k=2 best (0.90), k=3 = DIY / shops / fleets, DBSCAN flags the single largest account |
| n7 | `r'\W'` raw string (avoids a SyntaxWarning in newer Python). Top rule: F-150 oil filter + 5-qt jug → 1-qt bottle, 100% confidence (the engine takes 6 quarts) |
| n8 | Missing taglines do not blank out the TF-IDF description (guards against `title + NaN = NaN`). Examples: Camry front pads, Mustang cold air intake; user 20006, part 1061. SVD RMSE ≈ 0.65 |

**Before class:** upload `data/` to Google Drive as `Colab Notebooks/data/ds4e-me/`. The notebooks read from that folder.
