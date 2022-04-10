# W2L2 Part A
## Testing for over-identifying restrictions
### Over-identification
-  In the model $y_1=\beta_0+\beta_1y_2+\sum{k-1}_{j=1}\beta_{j+1}z_j+u$ if we have more instruments than endogenous regressors ($y_2$), we say that the parameters are over-identified.
-  Suppose we have $h+1$ instruments ($z_k,...,z_{k+h}$). Then we have $1+k+h$ moments. $E(u)=0$, and $cov(z_j,u)=0, j=1,...,k+h$, to identify (or solve for) $1+k$ parameters, with $h$ over-identifying restrictions. (We have $h$ more equations than unknown $\beta$'s)
-  If all instruments are valid (IE & IR), 2SLS is consistent, and the 2SLS residual will not be correlated with exogenous $z$’s.
- If 2SLS residual is found to be correlated with $z$’s., one or more instruments must be actually endogenous
- This idea can be use to test the exogeneity of the instruments
## Residual-based over-identification test
- Suppose we have $h+1$ instruments ($z_k,...,z_{k+h}$) with $h>0$. The null hypothesis is that all instruments are exogenous (IE).
1. Estimate $y_1=\beta+\beta_1y_2+\sum^{k+1}_{j=1}\beta_{j=1}z_j+u$ using 2SLS, and save the 2SLS residual as $\hat u$
2. Regress $\hat u$ on ($z_1,...,z_{k+h}$), save the R-squared as 𝑅2, and compute $R^2_\alpha$, and compute $nR^2_\alpha$
- Decision rule: Reject the null when $nR^2_\alpha$ is too large (exceeds $\chi^2_h$ critical value, $df=h$)
- Example 15.8 (MROZ.dta). There are two instruments (fatheduc, motheduc) for educ. `regress uhat exper expersq motheduc fatheduc` yields $nR^2_\alpha=0.378$ with p-value$=0.539$. We cannot reject the null that (fatheduc, motheduc) are exogenous.
## More on over-identifying restrictions
### When having multiple instruments
- When instruments are exogenous, the more instruments the better asymptotically, as over-identifying restrictions improve asymptotic efficiency of the 2SLS estimator
- However, if IE does not hold for some instruments, the 2SLS becomes inconsistent, and biased asymptotically. Hence, the over-identification test is important.
- For finite samples, increasing the number of instruments (even if IE holds) can result in severe bias in 2SLS, see Bound, Jaeger & Baker (1995).
- It is informative to compare 1-instrument-based 2SLS estimates against all-instrument-based 2SLS estimate. A large difference would be an indication that one or more instruments are invalid
### 2SLS with heteroskedasticity
- Similar to OLS context, heteroskedasticity means that $var(u|z_1,...,z_{k+h})$ depends on the $z$'s
- Bruesch-Pagan het-test can be applied to 2SLS residual (Ch8.3)
- The presence of heteroskedasticity invalidates the 2SLS se’s and efficiency.
- Het-robust se’s should be used for inference. Stata has built in capacity to do so, with option `vce(r)` or `robust`
- We can estimate the conditional variance of $u$ based on the 2SLS residuals (Ch8.4)
- Then, similar to the WLS or FGLS used in Ch8, we divide all variables ($y_1,y_1,1,z_1,...,z_{k+h}$) by the square root of the conditional variance of $u$, and apply 2SLS to the transformed data (Ch8.4)
## 2SLS example
### Example (MROZ.dta, working women)
- Interested in return to schooling, how lwage is related to educ. Main concern is that educ may be correlated with the error term, which includes unobserved abil.
- Assume exper is exogenous. The model of interest is $lwage=\beta_0+\beta_1educ+\beta_2exper+\beta_3expersq+u$
- OLS would over-estimate the coef on educ if educ is positively correlated with abil, and abil has a positive effect on wage
- ![[Screen Shot 2022-04-07 at 9.27.04 pm.png|500]] 
-  Potential instruments (motheduc, fatheduc, kidslt6) should be uncorrelated with u but correlated with educ
- (Given educ & other z’s, the instruments have no effect on lwage)
- First we check instrument relevance by regressing educ on (exper, expersq, motheduc, fatheduc, kidslt6)
- (motheduc, fatheduc, kidslt6) appear jointly significant.
- Save the residual from the reduced-form equation as `vhat`
![[Screen Shot 2022-04-07 at 9.31.16 pm.png|500]]
- Using (motheduc, fatheduc, kidslt6) as instruments, 2SLS estimate on educ appears smaller than OLS, 6.0% vs 10.7%.
- 2SLS se (.030) is more than twice of OLS se (.014)
- Save 2SLS residual from the structural equation as `uhat`
- ![[Screen Shot 2022-04-07 at 9.32.23 pm.png|500]]
-  We test the null that educ is exogenous by regressing lwage on (educ, exper, expersq, vhat).
- The t-stat (p-value) on vhat indicates the null can be rejected at the 10% level of significance
- As 2SLS and OLS estimates are very different, educ is treated as endogenous.
- ![[Screen Shot 2022-04-07 at 9.33.48 pm.png|500]]
-  We test the null that (motheduc, fatheduc, kidslt6) are exogenous by regressing uhat on (exper, expersq, motheduc, fatheduc, kidslt6)
- The over-identification test does not reject the null (p-value = .81)
- ![[Screen Shot 2022-04-07 at 9.34.40 pm.png|500]]
- Het-robust se’s should be used. This is done in Stata by option `vce(r)`. The het-robust se’s are larger than usual 2SLS.
- The coefficient on educ is only statistically significant at 7%. The return to schooling estimate is practically much smaller than OLS (6.0% vs 10.7%). Its importance is less than what OLS suggests.
>  Breusch-Pagan het test: LM = 13.5, p-value = .019
- ![[Screen Shot 2022-04-07 at 9.36.54 pm.png|500]]

# W2L2 Part B
## FAQ on IV and 2SLS
### How and where do we find IVs?
> $y_1=\beta_0+\beta_1y_2+\sum^{}/k-1_j=1\beta_{j+1}z_j+u$
-  “Variables in the other equation”. E.g., to estimate demand curve, IVs can be variables that shift supply but do not affect demand
- “Policy changes”. E.g., a change in tuition affects rate of college attendance but is uncorrelated with unmeasured student ability
- “Randomly assigned variables”. E.g., draft lottery outcome affects probability of military service, but is uncorrelated with ability
- “As good as random.” E.g., Policies may differ across regions or time in ways that are exogenous to individuals.
### In what contexts is IV estimation used?
- Omitted variables, or serious measurement errors in regressors
- Self-selection (eg. choose to attend college based on one’s ability)
- The dependent variable and the regressors are simultaneously determined (Ch16)
- Any source of correlation between the error term and regressors
### What are threats to the validity of IV estimation?
- Instrument exogeneity assumption can be hard to defend. We need to have solid economic reasons for the IE assumption
- Instrument relevance can be affected by “weak IVs” that only have small correlation with the endogenous regressor. The IR can be checked in the first stage regression in 2SLS (Ch15.3c)
### Can we test instrument exogeneity?
- No, if we only have one IV for each endogenous regressor
- Yes, if we have more IVs than endogenous regressors (over- identification test, Ch15.5b). But we have to assume one instrument is valid
### Can we test if a regressor is endogenous?
• Yes, if we have a valid instrument (endogeneity test, Ch15.5a)
# W3L1 Part A
## Intro
###  Simultaneity: a fundamental source of endogeneity
-  In some applications, the violation of MLR4’ is the consequence of omitted variables or measurement error
- The issues with omitted variables or measurement error, which lead to OLS estimation bias, can be viewed as data issues
- If we had better or more complete data, these issues would not arise
	- eg. If variables were accurately measured, ... 
	- eg. If we had good proxies for omitted variables, ...
- However, in many applications, even when we have perfect data (with no measurement error and no omitted variables), MLR4’ can still be violated owing to simultaneity
- Simultaneity refers to cases where variables of interest are jointly determined by an “equilibrium” mechanism
### Examples of simultaneity
-  Being single or being smelly: which is cause?
- Health or exercise: which is cause?
### Examples of simultaneity bias
- How do we estimate the demand curve for a good? Regressing quantity sold on price is not valid, because the existence of a supply curve makes price endogenous. (See lemonade example)
- An increase in police force may reduce crime rate. But the police force budget may be increased if the crime rate rises. OLS regression of crime on police may be confounded
- Are happier people healthier? One possibility: happiness makes people healthy. The other possibility: health makes people happy
- When two variables are simultaneously determined, they are called endogenous variables, as they are entangled “inside” a system. This is a deeper source of endogeneity than omitted variables
- The symptom of simultaneity is the violation of MLR4(4’). Hence, IV and 2SLS are applicable in Simultaneous Equation Models (SEM)
## Supply and Demand as an SEM (Simultaneous equation model)
### Example: Supply and Demand for a good
> Behaviour of producers and consumers
- Supply: $q_s=\alpha_1\rho+\beta_1z_1+u_1$
- Demand: $q_d=\alpha_2\rho+\beta_2z_2+u_2$
- Equilibrium: $q_s=q_d$, Solve for $\rho$ that equates supply and demand
	- $q_s (q_d)$: quantity supplied (demanded), $\rho$= market price
	- $q_s=q_d$: quantity supplied= quantity demanded
	- $z_1$: observed supply shifter
	- $z_2$: observed demand shifter
	- $u_1(u_2)$: unobserved supply (demand) shifter
	- $\alpha_1(\alpha_2)$: supply (demand) elasticity if ($q,p$) are in log form
- Example: $z_1$ could be input prices that affect the cost of making the product (like the prices of lemons and sugar for lemonade)
- Example: 𝑧2 could be season of the year, as many goods like lemonade and women’s dresses have seasonal demand patterns
### A couple notes on notation:
-  When discussing SEM we will often drop the intercepts to simplify the algebra – as above. Remember you can always get rid of intercepts by de-meaning variables before regression
- The demand curve is often written with price on the left, as in:
	- $\rho=\frac{1}{\alpha_2}q-(\frac{\beta_2}{\alpha_2})z_2-\frac{1}{\alpha_2}u_2$
- $\alpha_2<0$ because “Demand curves slope down” when we plot p on the y-axis and q on the x-axis
- Suppose that we observe the quantity transacted $q_t$, average price $p_t$, supply shifters ($z_{i1}$) and demand shifters ($z_{i2}$) at each time t
- We are interested in estimating the parameters in
	- Supply: $q_t=\alpha_1\rho_t+\beta_1z_{t1}+u_{t1}$
	- Demand: $q_t=\alpha_2\rho_t+\beta_2z_{t2}+u_{t2}$
- The two equations form a simultaneous equations model (SEM)
- Both supply and demand equations are called structural equations
- As ($q,p$) are simultaneously determined in the market, they are endogenous variables
- The parameters ($\alpha_1,\alpha_2,\beta_1,\beta_2$) are called structural parameters
- The demand and shifters ($z_1,z_2$) are exogenous as they are determined outside the model. They are assumed to be uncorrelated with the structural errors ($u_1,u_2$)
## Two way Causality as an SEM
###  Example 16.1. City authorities want to know if increasing the number of police reduces murder rates:
- $murdpc=\alpha_1polpc+\beta_{11}incpc+u_1\hspace{3em}(1)$ 
- murdpc: murders per capita
- polpc: police offers per capita
- inpc: income per capita
-  Can we think of polpc as being determined without considering murdpc Probably not, as a high murder rate may cause a city to hire more police. Thus, polpc is likely to be endogenous
- A city’s (budget) behaviour may be described as
	- $polpc=\alpha_2murdpc+\beta_{21}otherFactors+u_2\hspace{3em}(2)$ 
-   To answer the question, we are interested in (1) only, but it is part of a SEM that consists of both (1) and (2).
## The nature of SEM
###  In a SEM, observed variables are classified into two categories, endogenous variables ($y$) and exogenous variables ($z$): 
> $y_1=\alpha_1y_2+\beta_1z_1+u_1$
> $y_2=\alpha_2y_1+\beta_2z_2+u_2$

- The endogenous variables are simultaneously determined by an “equilibrium” mechanism inside the model.
- The exogenous variables are determined outside the model, and are uncorrelated with the structural errors ($u_1$,$u_2$)
- The number of equations = the number of endogenous variables
- We often focus on one of the equations. If an endogenous variable appears as a regressor (on the right-hand side of the equation) then it will be correlated with the error term.
- The exogenous variables ($z_1$,$z_2$) are important as they enable us to identify the structural parameters (see below)
## Simultaneity bias in OLS
- Consider the SEM, where the first equation is of interest,
> $y_1=\alpha_1y_2+\beta_1z_1+u_1$
> $y_2=\alpha_2y_1+\beta_2z_2+u_2$

- Statistically, $z$ being exogenous means that $cov(z_j,u_k)$ for any $j=1,2$ and $k=1,2$
- We can solve the SEM to express $y_1$ in terms of $z$ and $u$, as long as $1-\alpha_1alpha_2\neq0$ (see 16.12-14)
  
> $\Large y_2=\frac{\alpha_2\beta_1z_1+\beta_2z_2+\alpha_2u_1+u_2}{1-\alpha_1\alpha_2} = \pi_{21}z_1+\pi_{22}z_2+v_2$ 

- Notice that $v_2=\alpha_2u_1+u_2$ so $y_2$ depends on $u_1$
- Thus $y_2$ is generally correlated with $u_1$ (unless $\alpha_2=0$)
- Hence, OLS estimation of the first structural equation is biased. This bias is known as simultaneity bias.

## Reduced form equations
> Structural equations:
> $y_1=\alpha_1y_2+\beta_1z_1+u_1$
> $y_2=\alpha_2y_1+\beta_2z_2+u_2$
- Continue with the SEM
	- Provided that $1-\alpha_1\alpha_2\neq0$, we can solve the SEM to express $y_2$ in terms of $z$ and $u$,
	> $\Large y_2=\frac{\alpha_2\beta_1z_1+\beta_2z_2+\alpha_2u_1+u_2}{1-\alpha_1\alpha_2}=\pi_{21}z_1+\pi_{22}z_1+v_2$ 

	-  We can also solve the SEM for $y$ in the same way:
	> $\Large y_1=\frac{\beta_1z_1+\alpha_1\beta_1z_2+u_1+\alpha_1u_2}{1-\alpha_1\alpha_2}=\pi_{11}z_1\pi_{12}z_2+v_1$

	-  The above two equations are called the reduced form equations, where each equation involves exactly one endogenous variable, and generally involves all exogenous variables
	- The parameters $(\pi_{11},\pi_{12},\pi_{21},\pi_{22})$ are reduced form parameters that are functions of the structural parameters (see next page)
	- The reduced form errors are generally correlated: $cov(v_1,v_2)\neq0$. The $z$’s are uncorrelated with the $v$'s
##  Mapping from reduced form to structural parameters
- Under what conditions, can we recover structural parameters from reduced form parameters?
- Structural equations:
	- $y_1=\alpha_1y_2+\beta_1z_1+u_1$
	- $y_2=\alpha_2y_1+\beta_2z_2+u_2$
- Reduced form equations:
	- $y_1=\pi_{11}z_1+\pi_{12}z_2+v_1$
	- $y_2=\pi_{21}z_1+\pi_{22}z_2+v_2$
- Reduced form and structural parameters are related by:
	- $\pi_{11}=\frac{\beta_1}{1-\alpha_1\alpha_2}$,    $\pi_{12}=\frac{\alpha_1\beta_2}{1-\alpha_1\alpha_2}$
	- $\pi_{21}=\frac{\alpha_2\beta_1}{1-\alpha_1\alpha_2}$,    $\pi_{22}=\frac{\beta_2}{1-\alpha_1\alpha_2}$
-  Because $z$’s are uncorrelated with the reduced form errors, we have $cov(z_j,v_k)=0$ for $j=1,2$ and $k=1,2$
• Hence, OLS may be used to consistently estimate the reduced form parameters.
# W3L1 Part B
## Identification
### Consider the SEM (Labour supply and demand in agriculture):
- Supply: $h=\alpha_1w+\beta_1z_1+u_2$
- Demand: $w=\alpha_2h+\beta_2z_2+u_2$
-  Suppose $z_1$ is manufacturing sector wage, and $z_2$ is land area in a county. 
	- (More land creates more demand for farm labour at a given wage)
- Assume the first equation is the supply curve ($\alpha_1>0$), and the second the demand for labour ($\alpha_2<0$). “Demand curves slope down.”
- For fixed $z_2$, the supply curve shifts when $z_1$ varies, which does not affect the demand
- Thus, variations in $z_1$ trace out (identify) the demand curve
- Similarly, variations in $z_2$ identify the supply curve
  ![[Screen Shot 2022-04-09 at 11.33.33 am.png|300]]
## Identification via Reduced Form Parameters
- Compare the structural and reduced form equations:
	- Structural:
		- $y_1=\alpha_1y_2+\beta_1z_1+u_1$
		- $y_2=\alpha_2y_1+\beta_2z_2+u_2$
	- Reduced form:
		- $y_1=\pi_{11}z_1+\pi_{12}z_2+v_1$
		- $y_2=\pi_{21}z_1+\pi_{22}z_2+v_2$
- The reduced form parameters can be consistently estimated by OLS. They are identified (as they can be expressed in terms of the moments of observed variables)
- The reduced form and structural parameters are related by:
> $\Large\pi_{11}=\frac{\beta_1}{1-\alpha_1\alpha_2}$,    $\Large\pi_{12}=\frac{\alpha_1\beta_1}{1-\alpha_1\alpha_2}$,           We may view $\pi$'s as observable quantities
> $\Large\pi_{21}=\frac{\alpha_2\beta_1}{1-\alpha_1\alpha_2}$,    $\Large\pi_{22}=\frac{\beta_2}{1-\alpha_1\alpha_2}$

- Thus, if we can solve these to express ($\alpha_1\beta_1\alpha_2\beta_2$) in therms of ($\pi_{11},\pi_{12}\pi_{21}\pi_{22}$), the structural equations are also identified
• If $z_1=z_2$, so only two $\pi$’s in the reduced form, we can’t identify the structural parameters

> Structural equations
> $y_1=\alpha_1y_2+\beta_1z_1+u_1$
> $y_2=\alpha_2y_1+\beta_2z_2+u_2$ 

- Identification via reduced form parameters (continued)
- Specifically, we find (when denominators are non-zero)  
> $\Large\alpha_1=\frac{\pi_{12}}{\pi_{22}},\alpha_2=\frac{\pi_{21}}{pi_{11}},\beta_1=\pi_{11}(1-\alpha_1\alpha_2),\beta_2=\pi_{22(1-\alpha_1\alpha_2)}$

- To identify the parameters of the first structural equation, the following conditions are required:
1. The reduced form equations exist $[1-\alpha_1\alpha_2\neq0]$ (Instrumental exogeneity, exclusion)
2. The second equation includes an exogenous variable that is excluded from the first equation (order condition) (Instrumental exogeneity, exclusion)
3. The excluded exogenous   is relevant in the second equation $\beta_2\neq0$ or $\pi_{22}\neq0$ (rank condition) (Instrumental Relevance)
-  Similarly, we can state analogous conditions for identifying the parameters of the second structural equation

- Now, suppose we have more exogenous variables
- $y_1=\alpha_1y_2+z_1\beta_1+u_1$
- $y_2=\alpha_2y_1+z_2\beta_2+u_2$
	where $z_1$ and $z_2$ may contain more than one exogenous variables
-  To identify the parameters of the first structural equation, the following conditions are required
1. The reduced form equations exist $1-\alpha_1\alpha_2\neq0$
2. The second equation includes at least one exogenous variable that is excluded from the 1st equation (order condition)
3. The excluded exogenous variables have non-zero coefficient in the 2nd (structural or reduced) equation. (rank condition)
- Similarly, we can state analogous conditions for identifying the parameters of the second structural equation.

### Example 16.3 (married female worker labour supply)
-  Structural equations – labor supply and demand:
	- $hours=\alpha_1lwage+\beta_{10}+\beta_{11}educ+\beta_{12}age+\beta_{13}kids6+\beta_{14}nwinc+u_1$,
	- $lwage=\alpha_2hours+\beta_{20}+\beta_{21}educ+\beta_{22}exper+\beta_23expersq+u_2$
- Here $hours$ and $lwage$ are endogenous. Other variables are assumed to be exogenous
- Assume that once $lwage$, $educ$, $age$, $kids6$, and $nwinc$ are controlled for, $exper$ and $expersq$ have no effect on $hours$ supplied
- The first equation is the labour supply equation, where $exper$ and $expersq$ are excluded. The order condition holds by assumption
- The rank condition for identifying the first equation is that at least one of the coefficients on (exper, expersq) is non-zero in 2nd eqn
-  Similarly, the rank condition for identifying the 2nd equation is that at least one of the coefficients on (age, kids6, nwinc) is non-zero
- The rank condition for identifying the first equation can also be stated in terms of the reduced form parameters, i.e., in the reduced-form lwage equation
	- $\Large lwage=\pi_{20}+\pi_{21}educ+\pi_{22}exper+\pi_{23}expersq$ 
		- at least one of the coefficients on (exper, expersq) is non-zero
- Similarly, the rank condition for identifying the 2nd equation can be stated as that at least one of the coefficients on (age, kids6, nwinc) is non-zero in the reduced-form hours equation.
## Estimation of SEM by 2SLS
### Estimate one equation at a time using 2SLS
- Example 16.3 (married female worker labour supply)
	- Structural equations
		- $hours=\alpha_1lwage+\beta_{10}+\beta_{11}educ+\beta_{12}age+\beta_{13}kids6+\beta_{14}nwinc+u_1$
		- $lwage=\alpha_2hours+\beta_{20}+\beta_{21}+\beta_{22}exper+\beta_{23}expersq+u_2$
	- Based on what we learned in Ch15, the first equation can be estimated by 2SLS using (exper, expersq) as instruments
	- Similarly, the second equation can be estimated by 2SLS, using (age, kids6, nwinc) as instruments
- In general, when the order and rank conditions (counterparts of IE, Exclusion, and IR) are satisfied for a structural equation, that equation can be estimated by 2SLS. The tools we learned in Ch15 are applicable here
- This is the most commonly used approach

## Less popular SEM estimation method
- Indirect least squares (ILS) – Note: This is the method we explained first, but it is not as common as 2SLS
	- When structural parameters are identified (being unique functions of reduced form parameters), they can be estimated as functions of reduced-form estimates, aka ILS. Consider the example:
		- Structural equations
			- $y_1=\alpha_1y_2+\beta_1z_1+u_1$
			- $y_2=\alpha_2y_1+\beta_2z_2+u_2$ 
		- Reduced form equations
		- $y_1=\pi_{11}z_1+\pi_{12}z_2+v_1$
		- $y_2=\pi_{21}z_1+\pi_{22}z_2+v_2$
	-  The structural and reduced form parameters are related by
		>$\Large \alpha_1=\frac{\pi_{12}}{\pi_{22}}, \alpha_2=\frac{\pi_{21}}{\pi_{11}},\beta_1=\pi_{11}(1-\alpha_1\alpha_2),\beta_2=\pi_{22}(1-\alpha_1\alpha_2)$

	- Step 1. OLS is used to obtain ($\Large \hat\pi_{11}\hat\pi_{12}\hat\pi_{21}\hat\pi_{22}$)
	- Step 2. ($\Large \hat\alpha_1,\hat\beta_1,\hat\alpha_2,\hat\beta_2$) are the functions of ($\Large \hat\pi_{11}\hat\pi_{12}\hat\pi_{21}\hat\pi_{22}$):
	  $\Large \hat\alpha_1=\frac{\hat\pi_{12}}{\hat\pi_{22}},\hat\alpha_2=\frac{\hat\pi_{21}}{\hat\pi_{11}},\hat\beta_1=\hat\pi_{11}(1-\hat\alpha_1\hat\alpha_2),\hat\beta_2=\hat\pi_{22}(1-\hat\alpha_1\hat\alpha_2)$
	- Step 3. Find SEs (This requires a bit of extra work)
## Estimation of SEM by 2SLS - example
- Example 16.5 (married women labour supply, MROZ.dta)
	- Structural equations
		- $hours=\alpha_1lwage+\beta_{10}+\beta_{11}educ+\beta_{12}age+\beta_{13}kids6+\beta_{14}nwinc+u_1$
		- $lwage=\alpha_2hours+\beta_{20}+\beta_{21}educ+\beta_{22}exper+\beta_{23}expersq+u_2$
	- The first equation: OLS estimate of $a_1$ is statistically and practically zero
	- Instruments (exper, expersq) are relevant with f-stat=9.33
	- ![[Screen Shot 2022-04-09 at 4.25.09 pm.png|400]]
	- ![[Screen Shot 2022-04-09 at 4.25.26 pm.png|400]]
	-  The first equation estimates: 2SLS differs markedly from OLS. The test in Ch15.5a confirms endogeneity of lwage (t-stat = -6.61). `vhat` is the residual from Stage-1 regression
	- Overidentification test (p-value = .35) does not reject the exogeneity of instruments
	- Supply elasticity = $\Large \frac{\hat\alpha_1}{average(hours)}=1.26$, i.e a 1% increase in wage leads to a 1.26% increase in hours work, ceteris paribus
	- ![[Screen Shot 2022-04-09 at 4.27.55 pm.png|400]] 
	- ![[Screen Shot 2022-04-09 at 4.28.06 pm.png|400]]
	-  The 2nd equation estimates: 2SLS and OLS are statistically insignificant. Test in Ch15.5a does not reject the exogeneity of hours for the second equation (t-stat = -0.74)
	- The key parameter estimate $\hat\alpha_2$ is statistically insignificant. An increase in hours supplied does not directly affect wage offered
	- Overidentification test (p-value = .26) does not reject that instruments are exogenous
	- ![[Screen Shot 2022-04-09 at 4.29.05 pm.png|400]]
	- ![[Screen Shot 2022-04-09 at 4.29.15 pm.png|400]]
	-  Similar conclusions also emerge from “$log(wage)$ vs $log(hours)$” specification. The elasticity of supply is positive, and that of demand is nearly zero (For hours spec, 41 fitted values < 0.)
	- Depiction of supply/demand at (0,0) with reduced-form residuals
# W3L2 Part A
## SEM with more than two equations
### Conditions for identification
- SEM can have more than two equations. For example
	- $y_1=\alpha_{12}y_2+\alpha_{13}y_3+z_1\beta_1+u_1$
	- $y_2=\alpha_{21}y_1+\alpha_{23}y_3+z_2\beta_2+u_2$
	- $y_3=\alpha_{31}y_1+\alpha_{33}y_2+z_3\beta_3+u_3$
> Number of equations = number of endogenous variables
> Each structural equation involves two or more endogenous variables
-  Order condition for the 1st equation: the number of excluded z’s is no less than the number of right-hand-side (RHS) endogenous variables. These excluded z’s are instruments for the 1st equation
- Rank condition for the 1st equation: the excluded z’s have explanatory power for any linear combination $ay_2+by_3$  for any $a$ and $b$ of RHS endogenous variables. The excluded z’s are relevant
- Under these conditions, the 1st equation is identified, and can be consistently estimated by 2SLS
-  Order condition for 1st equation: We have two endogenous variables in the first equation, so to identify their two coefficients we need at least two excluded instruments. (“Excluded” means not in $z_1$)
- Rank condition for the 1st equation: These excluded z’s have to generate independent exogenous variation in both $y_2$ and $y_3$
- Easiest way to understand the rank condition is to understand when it fails
	- (1) If the two excluded z’s are both significant predictors of $y_2$ but neither helps to predict $y_3$ then the rank condition fails, as the z’s don’t generate exogenous variation in $y_3$
	- (2) If the two z’s cause $y_2$ and $y_3$ to move in a perfectly correlated way the rank condition fails
### Conditions for identification: a little matrix algebra
- Consider the first structural equation
	- $y_1=y_2\alpha_1+z_1\beta_1+u_1$ where $y_2=[y_2,y_3],\alpha_1=[\alpha_{12},\alpha_{13}]',z_1=[1,z_1,...,z_{k_1}]$ and $\beta_1=[\beta_{10},\beta_{11},...,\beta_{k_1}$
- Write the reduced-form equation for the vector $y_2$ as $y_2=z_1\pi_{21}+z_2\pi_{22}+v_2$ where $z_2=[z_{k_1+1},...,z_k]$ is the vector of excluded instruments and $\pi_{21}$ and $\pi_{22}$ are coefficient matrices
- Order condition: # entries in $y_2$ $\leq$ # entries in $z_2$
- Trivial to check, but the exogeneity of $z_2$ is hard to establish
- Rank condition: the rank of $\pi_{22}$=# entries in $y_2$
- $z_2$ has predictive power for any linear combination of $y_2$

### Example: inflation, openness and income (OPENNESS.dta)
-  Romer (1993) argues that a country’s openness has a negative effect on inflation. His data set includes 114 countries: 
	- _inf_ average annual inflation (1973-91),  
	- _open_: average % share of imports in GDP (1973-91),
	- _pcinc_: per capita income in 1980 US dollars,
	- _land_: land area (square miles),
	- _oil_: 1 for oil producing country and 0 otherwise
- The equation of interest is:
	- $inf=\beta_0+\beta_1open+\beta_2log(pcinc)+u_1$ where $\beta_1$ is expected to be negative by Romer’s argument
	- At country level, (inf, open, pcinc) are likely jointly determined, and all three are treated as endogenous variables
	- Assume that (land, oil) are exogenous. (Two excluded z’s)
- So we have the following equations:
- $inf=\alpha_{12}open+\alpha_{13}log(pcinc)+\beta_{10}+u_1$, (structural)
- $open=\pi_{20}+\pi_{21}log(land)+\pi_{21}oil+v_2$, (reduced form)
- $log(pcinc)=\pi_{30}+\pi_{31}log(land)+\pi_{32}oil+v_3$ (reduced form_
	- where (inf, open, pcinc) are endogenous, and (land, oil) are exogenous
---- 
- The equation $inf=\beta_0+\beta_1open+\beta_2log(pcinc)+u_1$ is identified when:
	- (land, oil) are exogenous; (order condition or IE + exclusion)
	- (land, oil) generate independent variation in $open$ and $log(pcinc)$ (rank condition or instrumental relevance IR)
	- OLS gives statistically significant negative estimate of $\beta_1$
	- ![[Screen Shot 2022-04-10 at 7.17.21 pm.png|600]]
-----
- Order condition holds, two IVs for two endogenous variables
- To check the rank condition or IR, we regress open and log(pcinc) on (log(land), oil), respectively
- The results confirm that the rank condition holds:
	- _log(land)_ predicts open, and oil predicts _log(pcinc)_
	- $\pi_{22}=\begin{bmatrix}-7.61&-0.08\\& \\1.14&1.42\end{bmatrix}$ , Rank of $\pi_{22}=2$
	> Two columns for two endogenous variables: No column is zero; The two columns are distinct and not proportional
- ![[Screen Shot 2022-04-10 at 7.27.56 pm.png|600]]
----
 
- The rank condition is called the rank condition because it is about the rank of the $\pi_{22}$ matrix
- If we had only one endogenous variable and one instrument then the $\pi_{22}$ matrix would just be a scalar
- Then we would only need to check if that coefficient is significant in the first stage (i.e., the instrument relevance condition IR)
- So with one endogenous variable and one instrument the rank condition is simply the IR condition.
- (Note: With one endogenous variable and multiple z’s the $\pi_{22}$ matrix is just a vector, so no matrix to worry about then either)
- Similarly, with only one endogenous variable the order condition is simply the instrument exclusion and exogeneity (IE) condition
----
- Given the assumed exogeneity of (land, oil), the equation of interest is exactly identified (two IVs for two endogenous regressors)
- The 2SLS estimation, using (land, oil) as instruments, gives statistically significant (at 5% level) negative estimates of ($\beta_1,\beta_2$)
- These are quite different from OLS estimates, where $\beta_1$ is less negative and $\beta_2$ is statistically zero
- ![[Screen Shot 2022-04-10 at 7.30.36 pm.png|500]]
----
- We can also do a residual test if (open, log(pcinc)) are exogenous ($H_0$), by regressing inf on (open, log(pcinc), v2hat, v3hat)
- The test does not reject $H_0$ at the 5% level. But, given that 2SLS result is quite different from OLS result, we cannot rule out the possibility that open and log(pcinc) are endogenous
- The 2SLS result here is also quite different from the 2SLS result in Example 16.6, where log(pcinc) is treated as exogenous
- ![[Screen Shot 2022-04-10 at 7.31.50 pm.png|500]]