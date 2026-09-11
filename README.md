# notes

# notes

EUREX IM MODEL

IM_EUREX = IM_Market + IM_liquidity+im_suma

where 

im_market = max ((var_histo(pnl,99%)+CBA), var_stress(pnl,97.5%))

for var_histo
n =750 

for CBA, called orrelation berach adjustment, intended to take into account the uncertainty of the time_windows choice for the var computation, EUREX uses a resampling in order to quantify the discrepancy to thte full historic var commutation 

CBA = k * square root of 1/n * sum ((var_sample(99%) - varhisto(99%))square) when var_sample(99%)>var_histo(99%)

k = 1.84, n = sum of number of var_sample(99%)>var_histo(99%) , meaning the number of sample 

var_sample (99%) = var_histo(pnl where j<=i<=j+windows,99%)
windows = 300
j<= n-300

so for the var sub sample
firstly using 300d rolling windows
generate sub samples from 750 days 
for each sub sampbles, calculate var 99%

then keep only the sub_sample where var_sample >var_histo
calcualt n, which is the number of sub_sample which satissfait the above conditions

calculate diff of each var sample and the var_histo
square the diff and sum up
divide by n and square root
time 1.84 to get final cba 

for sterssed var 
using 250 stressed historical data
confidence level = 97.5

for im_liquidity
it represente the swap delta hedging cost in the case of default 

term structure: 2 5 10 15 20 30 50Y
hedging start from the longest term to shortest 

swap value V0 = (1-ZC) - K* sum(delta * DF)
where
ZC = exp(-rate * T)

ir sensi:
ir sensi = T * ZC * (1+K*delta)

hedging nominal = - delta / ir sensi

IM_Liquidity = sum (var(delta_hedge,99%)/(square root of 5)+delta/2*sum(abs(delta hedge)))

var (delta_hedge) represent the market risk, divide by sqaure root of 5 to present 1day var
bid-ask spread represent the b-a cost; delta is the bidask parameter 


suma component
suma represent the concentration risk 

im_suma = max(alpha_im_market * im_market -im_liquidity,0)

alpha = max(0.8 * min((x -200meur) / 800meur,1),0)

for alpha
if im_market < 200meur, alpha = 0
im_market between 200m to 1000meur, linear increase
im_market >1000 meur, alpha = 0.8

