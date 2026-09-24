# TrafficModeling
Probability and Statistical Modeling of Web Traffic



--> python run main.py





Probability and Statistical Modeling of Web Traffic 1
Assignment: Probability and Statistical
Modeling of Web Traffic
Using the WorldCup98 Web Server Logs Dataset
Course Topics Chapters 1-4 Recommended
Tool Python / Jupyter Notebook
Dataset WorldCup98 Web Server Logs Deliverables Notebook + short report
Scenario. You are a performance engineer analyzing real web traffic from the 1998 FIFA World Cup website.
Your goal is to use probability and statistical models to understand request arrivals, request behavior, and
the variability of web traffic.
Dataset
WorldCup98 Web Server Logs. This public dataset contains real HTTP request logs from the 1998 FIFA World
Cup website. It is especially useful for studying request arrivals, interarrival times, object sizes, request
categories, and event probabilities. Because it is a workload/access-log dataset rather than a latency
benchmark, this assignment focuses on arrival behavior and request characteristics rather than true server
response time.
Recommended Dataset Access (Zenodo)
Use the three-day WorldCup98 subset hosted on Zenodo for this assignment. It contains three compressed
access-log files (38.9 MB total) and is much more manageable than the complete archive. Dataset page: 1998
World Cup Website Access Logs - Zenodo
• TR1.gz - access traffic from May 20, 1998 (17.7 MB)
• TR2.gz - access traffic from May 4, 1998 (7.2 MB)
• TR3.gz - access traffic from May 23, 1998 (14.0 MB)
DOI: 10.5281/zenodo.5145855. The files are gzip-compressed binary logs. You should document any
preprocessing or conversion used before analysis.
Data Preprocessing and Starter Code
The Zenodo TR1.gz, TR2.gz, and TR3.gz files are gzip-compressed binary logs, not CSV or plain-text web logs.
After decompression, each request is stored as a fixed 20-byte binary record. Use the starter code below to
convert the records into a Pandas DataFrame before beginning the statistical analysis.
Probability and Statistical Modeling of Web Traffic 2
Binary Record Structure
Field Data type Size
timestamp uint32 4 bytes
client_id uint32 4 bytes
object_id uint32 4 bytes
size uint32 4 bytes
method uint8 1 byte
status uint8 1 byte
type uint8 1 byte
server uint8 1 byte
The four 32-bit integers are stored in big-endian (network) byte order, so the Python struct format used below
begins with >.
Required Preprocessing
1. Read and decompress one of the .gz files directly in Python.
2. Unpack each 20-byte request record.
3. Create a Pandas DataFrame containing timestamp, client_id, object_id, size, method, status, type, and
server.
4. Convert timestamp to a readable datetime.
5. Sort requests by timestamp.
6. Create derived variables needed for the statistical analysis, especially interarrival_time and request counts
per fixed time interval.
Starter Python Code
import gzip
import struct
import pandas as pd
records = []
with gzip.open("TR1.gz", "rb") as f:
#with open(file_path, "rb") as f: #if it is autoDecompressed in your MACOS
while True:
data = f.read(20)
if len(data) < 20:
break
timestamp, client, obj, size, method, status, file_type, server = \
struct.unpack(">IIIIBBBB", data)
records.append([
timestamp, client, obj, size,
method, status, file_type, server
])
Probability and Statistical Modeling of Web Traffic 3
df = pd.DataFrame(records, columns=[
"timestamp", "client_id", "object_id", "size",
"method", "status", "type", "server"
])
df["datetime"] = pd.to_datetime(df["timestamp"], unit="s", utc=True)
df = df.sort_values("timestamp").reset_index(drop=True)
print(df.head())
print(df.shape)
Create Variables for the Assignment
Interarrival time between consecutive requests:
df["interarrival_time"] = df["timestamp"].diff()
Number of requests per minute:
df["minute"] = df["datetime"].dt.floor("min")
requests_per_minute = df.groupby("minute").size().reset_index(name="request_count")
These derived variables connect directly to the probability models in the assignment: request_count can be
studied with a Poisson model, interarrival_time with Exponential models, and size with distributions such as
Lognormal.
Scope of Preprocessing
For this statistics assignment, you are not required to reverse-engineer every packed field. The timestamp,
client_id, object_id, size, and type fields are sufficient for most tasks. Decoding the detailed status and server
bit fields is optional unless you choose to use them in your analysis.
You may use TR1.gz alone for a smaller analysis, or combine TR1.gz, TR2.gz, and TR3.gz after preprocessing.
Clearly state which file(s) you used in your notebook and report.
Learning Objectives
• Define sample spaces, events, random variables, and probability models from observed web traffic.
• Use conditional probability, independence, total probability, and Bayes’ theorem.
• Build and interpret PMFs, CDFs, means, and variances for discrete random variables.
• Evaluate Binomial, Geometric, Negative Binomial, Hypergeometric, and Poisson models.
• Evaluate continuous probability models for interarrival time and object size, including Exponential, Normal,
and Uniform distributions.
• Compare empirical behavior with theoretical probability models and explain model limitations.
Part 1 - Probability from Observed Events
Define two events from the data. For example:
• A = {request belongs to a selected request category}
• B = {object size exceeds a chosen threshold}
1. Estimate P(A), P(B), P(A ∩ B), and P(A ∪ B). Verify the addition rule numerically.
2. Estimate P(A | B) and P(B | A). Interpret both probabilities in the context of web requests.
Probability and Statistical Modeling of Web Traffic 4
3. Investigate whether A and B appear independent. Support your conclusion with numerical evidence.
4. Partition requests into several request categories and use the law of total probability to compute the
probability of a selected event.
5. Use Bayes’ theorem to compute the probability that a request came from a particular category given that
another event occurred. Compare the theoretical result with the corresponding proportion measured
directly from the dataset.
Part 2 - Discrete Random Variables and Distributions
Create equal time intervals (for example, 1-minute intervals) and define X = number of requests arriving in one
interval.
6. Construct the empirical probability mass function (PMF) of X.
7. Construct the empirical CDF of X and use it to estimate probabilities such as P(X ≤ x) and P(a < X ≤ b).
8. Calculate the sample mean and variance of X.
9. Fit a Poisson model to X by estimating λ from the data. Compare the observed mean, observed variance, and
empirical frequencies with the theoretical Poisson model.
10. Define a binary request event and estimate its probability p. For groups of n requests, use a Binomial model
to calculate the probability of exactly k events, no events, and at least one event.
11. Define Y as the number of requests until the first occurrence of your selected event. Use a Geometric
distribution to model Y and compare the theoretical and empirical means.
12. Extend the previous question to the number of requests required to observe the third occurrence of the
event. Identify the appropriate distribution and calculate its expected value.
Part 3 - Continuous Random Variables and Distributions
Define T = interarrival time between consecutive requests.
13. Calculate the mean, variance, standard deviation, median, and selected percentiles of T. Plot a histogram
and empirical CDF.
14. Compare the interarrival-time data with at least three continuous distributions, including Exponential and
Normal.
15. Estimate the required parameters for each model and overlay the theoretical density on the observed
histogram.
16. Discuss which distribution appears to describe interarrival time most reasonably. Explain why real web
traffic may differ from idealized textbook models. (Bursty traffic, Automated Bots, Attacks, Day vs Night
variations)
17. Using your preferred model, estimate probabilities such as P(T > t0) and P(t1 < T < t2). Compare these
theoretical probabilities with empirical proportions.
18. Repeat part of the continuous-distribution analysis using object size as the random variable. In particular,
investigate whether a Lognormal model is reasonable.
19. Investigate whether a Uniform distribution is reasonable for any continuous variable or transformed subset
of the data. Explain your conclusion.
Part 4 - Normal Approximations
20. For a sufficiently large number n of requests, let Z be the number satisfying your selected binary event
(success/failure). Calculate P(Z ≥ k) using both the exact Binomial distribution and the Normal
approximation with continuity correction. Compare the results.
Probability and Statistical Modeling of Web Traffic 5
21. For a time interval with sufficiently large expected request count λ, calculate a request-count probability
using both the exact Poisson distribution and the Normal approximation. Report the difference.
Final Engineering Analysis
Write a 1-2 page interpretation answering the following question:
What have you learned about the behavior and variability of this web system from the data?
• Identify which probability models fit reasonably well and which do not.
• Explain possible reasons for deviations from theoretical models.
• Distinguish conclusions supported directly by the data from conclusions that depend on modeling
assumptions.
• Include at least two meaningful figures and one summary table in your final interpretation.
Deliverables
• One Jupyter Notebook (.ipynb) containing data preparation, calculations, plots, model comparisons, and
short interpretations throughout.
• One short report (PDF or DOCX) containing the 1-2 page final engineering analysis and selected
figures/tables.
• Code must be reproducible from the given dataset and from clearly documented preprocessing steps.
Suggested Grading
Component Weight
Probability and conditional probability 30%
Discrete random variables and distributions 30%
Continuous distributions and approximations 30%
presentation 10%
Submission Notes
• Use meaningful variable names and label all axes, tables, and figures.
• Show formulas or identify the probability model used before presenting numerical results.
• Do not report only numerical answers; briefly interpret each result in the context of the web system.
• When a theoretical distribution does not fit the data well, explain why rather than forcing the model.
