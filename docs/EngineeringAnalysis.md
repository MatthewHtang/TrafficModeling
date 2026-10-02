### Final Engineering Analysis
## Part 1 - Probability from Observed Events

- What conclusion can we make directly from the given data (TR1 file):
* The probabilities we calculated from P(A), P(B), P(A ∩ B), P(A ∪ B), P(A | B) and P(B | A) <br>   
  are just a pure calculation we got by dividing the count by total number of requests. <br>

* We have performed Addition rule, the law of total probability and Baye's theorem and they <br>
  all matched when you use counts. However this doesn't tell anything new about the web traffic. <br>

* We have also calculated whether event A and B are dependent/ independent. if P(A ∩ B) is clearly <br>
  different from P(A)*P(B), then this two events are connected.

- What depends on modeling assumption:
* For this assignment, we only use day 1 (TR1), saying that "this is how the World Cup website traffic <br>
  always behave", assuming that the other days always behave just like day 1.

* Calling event A and event B dependent in general: we only use day 1 data here. With millions of requests,<br>
  even a tiny differnce looks significant. 

* We treat the observed proportions as probabilities, which assumes that each requests are independent.<br> In reality, requests from the same page visit are clustered, so these are estimates from one day of data.

