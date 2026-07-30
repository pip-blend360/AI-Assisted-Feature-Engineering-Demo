# AIassistedFeatureEngineeringDemo

In this demo we are going to learn to use AI to create a feature engineering pipeline without writing any code. 

We are going to do this in two steps:
1. We will use a Claude plugin called Superpowers to write a plan
2. We will use CoCo (Cortex Code) to create the pipeline in Snowflake.

What you need to go through this demo is in the file Demo-Prep-Instructions 1.md.

The situation is retail, we want to create an exhaustive feature engineering pipeline to train customer-level models such as churn, Lifetime Value, and segmentation.

As usual with a large retailer the transactions data is not all in one table. The schema for this situation is in the background folder, data_dictionary.md. However, this is not intended for we human's to read. Let the AI do it.

During this demo we will only do the feature engineering, not feature selection, or model training.

Feature engineering is often a very tedious and time consuming process. Some describe data-wrngling to be 80% of the work during model training.

At the same time, in large retail situations I have had over 700 features usable to describe layers of customer behaviors.

(●'◡'●) Have Fun!
