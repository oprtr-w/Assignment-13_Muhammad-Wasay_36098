# 1_attempt.md — Initial Draft and Methodology

**Student:** Muhammad Wasay  
**ERP:** 36098

> Snapshot note: This is a pre-AI working draft outlining the planned calculations and initial thoughts.

## Planned approach

1. Load the Week 2 cleaned dataset and keep the two city series separate rather than pooling them together.

2. Compute both the arithmetic mean and the median of valid prices for every item–city combination.

3. Order each item–city group chronologically and determine the percentage change between the earliest valid price and the most recent valid price:

   $$
   \frac{\text{last valid price} - \text{first valid price}}{\text{first valid price}} \times 100
   $$

4. Run a sensitivity comparison using Sweetener D in City 1 by contrasting the cleaned Week 2 figure ($157.5$) against the uncleaned flagged figure ($9,999$).

5. Leave the missing observation for Pulse C in City 2 un-imputed. Base its percentage change on the earliest available record and explicitly note the one-week delay in its start date.

## Preliminary interpretation

Across most item–city pairs, the mean and median sit very close together, indicating that once extreme anomalies are cleaned, the central tendency of these sample prices is not heavily skewed. Still, the two measures behave differently: the mean incorporates the exact magnitude of every single data point, whereas the median simply identifies the middle observation in a sorted list.

Additionally, a first-to-last percentage change only captures the net shift between the two endpoints. It does not describe week-to-week volatility in between and should not be interpreted as a smooth, constant climb.

## Dataset notes

The cleaned dataset from Week 2 consists of 96 records. One observation remains missing (Pulse C, City 2 on 1 January 2026). Two extreme entries from the raw ledger were previously adjusted in Week 2: `1,250` for Staple A in City 2, and `9,999` for Sweetener D in City 1. The raw `9,999` figure is brought back solely to demonstrate how outliers distort averages.