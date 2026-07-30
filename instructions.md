Create a markdown file with all the needed instructions and the full process needed to generate the outcome stated in this prompt, make it as detailed as possible to avoid confusion and errors. If something is unclear, just ask:

# Goal

Create an autonomous, hands-on feature-engineering pipeline/workflow using Snowflake Cortex AI.

The purpose of the presentation is to teach people how to use AI to design, generate, document, and deploy a customer-level feature-engineering pipeline for churn or customer lifetime value (LTV) modeling.

The goal is to avoid writing code manually. Snowflake Cortex AI should suggest, generate, and deploy the SQL code inside Snowflake.

# Business Context

The pipeline must be built from a customer lifetime value and marketing perspective.

The dataset definitions will be provided as context. A markdown file will also be provided containing the specific features requested by the business.

The AI should use these inputs to generate the required customer-level features, and it may suggest additional useful features when appropriate.

The dataset may change over time. If the dataset changes, the generated features and their documentation should update accordingly.

# Pipeline Requirements

Create a Snowflake SQL-based data pipeline that develops customer-level features for churn or LTV models.

The pipeline should support hundreds of features if needed, including:

- Short-term features: 30-day and 90-day windows
- Long-term features: 1-year windows
- Additional relevant customer-level marketing, engagement, purchase, retention, churn, and LTV features

The pipeline must run regularly, likely daily, so it should be designed with repeatability, automation, and refresh logic in mind.

# Required Pipeline Versions

The pipeline must generate two versions of the dataset:

## 1. Training and Testing Dataset

This dataset is used to train and evaluate a model.

It must include:

- Target variable `y`
- Feature variables `X`
- Proper historical cutoff dates
- No data leakage

Example:

If today is July 17 and the model predicts customer purchases in the next 30 days:

- Target `y`: number of orders between June 17 and July 17
- Features `X`: customer-level features calculated only from data available on or before June 16

The pipeline must ensure that features are calculated only from data available before the target window begins.

## 2. Scoring Dataset

This dataset is used to make predictions for current customers.

It must include:

- Feature variables `X`
- No target variable, since the future outcome is unknown
- Features calculated using data available up through the scoring date

Example:

If today is July 17:

- Features `X`: customer-level features calculated using data available through July 17
- Predicted target period: July 18 through August 18

This dataset will be used to predict future customer behavior, such as purchase likelihood, churn risk, or expected customer value.

# AI Generation Requirements

Snowflake Cortex AI must generate all SQL code.

No manual SQL coding should be required.

The AI should:

- Read the dataset definitions
- Read the business-requested feature list from the markdown file
- Recommend additional useful features when appropriate
- Generate SQL for all feature calculations
- Generate SQL for the training/testing dataset
- Generate SQL for the scoring dataset
- Deploy the pipeline inside Snowflake
- Produce documentation describing every generated feature

# User Inputs Required at Runtime

When Snowflake Cortex AI runs, it must ask the user for:

- The target database and schema where the generated tables should be saved
- The names to use for the output tables
- The training/testing dataset configuration with different timeframes (30 days, 60 days, 1 year)
- The scoring dataset configuration
- Any business-specific modeling objective, such as churn, repeat purchase, or LTV


# Agent-Facing Run Log

The process must also create an agent-facing document summarizing the full pipeline run.

This document should be designed for another AI agent to consume and verify what was done.

It should include:

- Inputs provided
- Dataset definitions used
- Business feature file used
- Features generated
- SQL objects created
- Tables created
- Runtime parameters
- Validation checks performed
- Any warnings, assumptions, or unresolved issues
- Evidence that the training/testing dataset avoids leakage
- Evidence that the scoring dataset uses only currently available data

# MVP Definition

The MVP is a fully autonomous Snowflake-based feature-engineering pipeline that can generate SQL, create feature tables, build training/testing and scoring datasets, and document the entire process using only the provided input files.

The MVP should work even if the input dataset changes, adapting the generated features and documentation accordingly.

# Final Deliverables

The final output should include:

- A deployed Snowflake SQL pipeline
- A training/testing dataset table
- A scoring dataset table
- Generated SQL code
- Feature documentation
- Agent-facing pipeline run log
- Any assumptions or recommendations for future improvements