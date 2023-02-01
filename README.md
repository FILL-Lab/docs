# FILL：A Filecoin Liquidity Pool for Storage Providers
## Abstract
Storage Providers (SPs) in the Filecoin network play critical roles in storing data and maintaining network security. In return, SPs earn transaction fees and block rewards, the probability of which is proportional to the amount of storage the storage provider contributes to the Filecoin network. Like most of the permissionless blockchain networks, one of the security mechanisms for the Filecoin is an upfront investment in resources, the Initial Pledge Collateral. In order to participate in the consensus, certain amount of FIL is needed by SPs ahead of the time, which forms the market demand of FIL borrowing for storage providing.

FILL is designed as a liquidity pool that will be implemented on FVM (Filecoin Virtual Machine) as a fully open-sourced, decentralized, algorithm-based lending platform. FILL facilitates a market for FIL token holders to deposit for passive income, and for SPs to borrow FIL for storage providing. 

This document serves as a White Paper, which describes the features and functionality of the FILL liquidity pool. It is an overview of the background and demand for the FIL lending market, technical architecture and economic design of the lending protocol, governance as well as other administrative aspects. The Introduction outlines the background information of Filecoin network and the characteristics of the FILL liquidity pool, which justifies the rationale of FIL lending for storage providing. Design Architecture describes the structure of the smart contract and how the protocol is implemented. The borrowing interest rate determination, deposit income distribution and governance token design are discussed under the Economics section, followed by Governance, Risk Management and Roadmap. 
## Introduction
### 1.1 Background
The Filecoin network is a decentralized storage marketplace developed on the basis of IPFS technology. It constructs decentralized consensus underlay through blockchain technology, making it possible for anyone to provide storage and trade storage spaces. Additionally, through Proof-of-Replication and Proof-of-Spacetime, Filecoin securely stores data and achieves data storage verifiability without the presence of centralized trust institutions. Moreover, the mechanisms of pledge collaterals and delayed rewards distribution assist Filecoin to discipline SPs, guarantee the security of storage while ensure the quality of service.

Since Filecoin data is secured by pledge collaterals, it is necessary for SPs to pledge Filecoin tokens to the network, no matter committing storage space (CommitCapacity) or storing useful data (RealDeal), which is known to be very capital intensive. For a long time during the development of Filecoin, the capital footprint of the pledge was much higher than the cost of the device itself. Providers have been carrying great financial burdens to serve the network. In this case, SPs are eager to borrow from service providers who own Filecoin tokens and pay a certain amount of interest. Such service providers that offer lending services already exist in the market but with many shortcomings.

In context of the public blockchain, service providers may require the use of other token as collateral. However, due to the volatile nature of digital currencies, lenders usually require an overcollateralization of 150% or more and trigger a liquidation in the event of significant devaluation of the collateral. Furthermore, lending interest rates in current market are very high and cannot reflect the market condition quickly.

Another option is to collateralize the SP’s service node itself since the service node holds enough pledged tokens. Additionally, the node receives block rewards in proportion to its Quality Adjusted Power (QAP) under storage mining. As a reasonable collateral asset, service node contains both pledges and rewards, which worth more than the value of the borrowed FIL token. However, such collagenization requires the SPs to surrender their control of service node, which brings in significant risks. For example, when conflicts arise between lenders and borrowers, it can lead to catastrophic consequences -- all pledges and proceeds are turned into nothing.

In such a high-risk high-yield market, service providers with impeccable credibility are essential to provide credit and guarantee services. The complexity of Filecoin technology makes it even more difficult for a traditional finance institution to handle. Despite the design of the Filecoin network itself being an ideal scenario for lending, the Filecoin lending market has not been well developed and is filled with challenges.

On the other perspective, a large number of FIL holders are eager to earn passive income by entering the lending market. Currently however, there are only centralized service providers undertake the business and gain the most from it, while the FIL holders who actually provide the fund were distributed with merely a small portion of the interest. As a result, the development of a trustless FIL lending protocol, which balances the benefits for providers and the costs for borrowers, is imminent under the current context.
### 1.2 What is FILL
FILL is a liquidity pool, which will be implemented on FVM (Filecoin Virtual Machine) as a fully open-sourced, decentralized, algorithm-based lending platform. FILL facilitates a market for FIL token holders to deposit for passive interest income, and for SPs to borrow FIL for storage providing.

To distinct from other lending markets, FILL does not require storage providers to collateralize Fiat currencies or other digital assets, instead, the storage providers’ existing account balance and future income would be acting as the collateral. Specifically, by taking over the storage provider’s Beneficiary Address, the potential income from storage mining is pledged by the protocol before the payback. 

The borrowing interest rate is not pre-defined or fixed, but dynamically adjusted naturally by the market condition, i.e., supply and demand. Higher demand in borrowing FIL builds up higher utilization rate for the liquidity pool, which pushes up the borrowing interest rate, while higher amount of FIL deposited into the pool reduces the utilization rate and pulls down the borrowing interest rate.

With the design, entry barrier is significantly lowered not only for the SPs to borrow FIL for power growth, but also for FIL token holders to make deposit, regardless the amount of FIL committed. As a bridge between the token holders as lenders, and storage providers as borrowers, FILL is expected be providing unprecedented liquidity in Filecoin market. It would also contribute the Filecoin ecosystem’s overarching goal of incentivizing a global network of computer operators to provide file sharing and storage services by enhancing the liquidity of FIL and further reducing the cost of storage power growth.
## Design Architecture
The subject of the design is a smart contract that executes transactions on the blockchain automatically. The proposed smart contract is a liquidity pool and a lending market, which calculates interest rate calculations, manages liquidity and provides clearing and settlement. This smart contract mainly interacts with two types of users: 1) lenders, which are usually the holders of the FIL tokens that are ready to earn interest; 2) borrowers, which are the storage providers who are willing to borrow funds to meet the collateral requirement for storage power growth.
<p align="center">
<img width="1107" alt="Screen Shot 2023-02-01 at 1 45 30 PM" src="https://user-images.githubusercontent.com/29261438/215963591-90361ee9-89b9-462d-84ed-412d29e8c345.png">


Figure 1[^1]: Relationship between lenders, borrowers and the liquidity pool
[^1]: Icon image: Flaticon.com

### 2.1 Deposits and Redemptions by FIL Token Holders
FILL Liquidity Pool smart contract is an open contract that allows any market participants to deposit/withdraw any amount of FIL assets directly into/from the contract at any time. Whenever a Filecoin token holder successfully deposited FIL into the contract, a certificate of interest would be awarded. This process can also be seen as a purchase/redemption activity of the certificate token FLE. Minting of FLE is acting as a proof of deposit and a vehicle of dividend allocation.

The amount of the certificate token awarded depends on the amount of liquidity provided, the total equity outstanding at certain point of time, total amount of FIL tokens deposited in the liquidity pool (including both the total amount deposited by the liquidity providers and the amount of interest repaid) and pool’s utilization rate. The calculation of the exchange rate between FIL and certificate token is detailed in the section 3.2 Deposit Benefit. 

Deposit and withdraw activities are always open and are protected by mechanisms that prevent FILL from draining out of liquidity. When the liquidity dries up, in other words, the utilization rate hikes to certain level, the amount of certificate token received in return of one unit of FIL token deposited increases exponentially, which stimulates liquidity providing. Similarly, the cost of certificate token FLE to withdraw one unit of FIL increases drastically so that the redemption activity would be discouraged. 

### 2.2 Borrowings and Repayments by Storage Providers
In addition to deposits and redemptions, FILL Liquidity Pool smart contract also processes requests for borrowings and repayments. The SPs with financing needs are also able to borrow FIL tokens from the smart contract by pledging their account balance and future income. 

The maximum amount of FIL a Storage Provider can borrow is associated with its pledgable account balance. In the existing lending protocols, over collateralization such as 150% or even 200% is required to protect the investors and the protocols from default. Usually, the collaterals are the one or a few of other mainstream cryptocurrencies. FILL Liquidity Pool smart contract basically follows the similar risk management tactic but instead, taking SP’s account balance as collateral. The SPs have options to choose the portion of account balance they would like to pledge and, exchange for the corresponding loan quota. Due to the special characteristics of the Beneficiary Address[^2], the future income from Storage Providing, on top of the existing account balance, would also be acting as collaterals to mitigate the default risks. As a result, the current minimum collateralization ratio could be set as 150% to for initiation. As soon as borrowing request received, the SP’s Beneficiary Address began to transfer to smart contract’s address. Starting this point, account balance and all future earnings of the SP will be pledged by the smart contract as collateral. Whenever the pledging process accomplished, the SP would receive the FIL token that intended to borrow. 

The interest is calculated by the smart contract automatically and determined solely by the utilization rate. Utilization rate is the total amount of FIL borrowed in a share of total liquidity in the FIL lending pool at a certain point of time. High utilization reflects high demand in liquidity pool, which introduces high borrowing interest rate. Low utilization indicates low demand, which supports low cost of capital to attract borrowers. The borrowing rate model allows for calibration of interest rates at key points and the parameters should be mutually decided by DAO. The loan interest is compounded continuously and accrued every epoch, but is paid along with the loan principal repayments. The detailed discussion on the borrowing interest rate model is outlined in section 3.1 Borrowing Interest Rate.

When borrowers request to repay the loan, principal and interest payments are made together through the smart contract. The repayments, both principal and interest are filled back in the liquidity pool and available for other SPs to borrow, which relieves the utilization rate pressure. 

[^2]: Only the Beneficiary role would be able to withdraw FIL from an SP node. [FIP-0029](https://github.com/filecoin-project/FIPs/blob/master/FIPS/fip-0029.md) proposed to re-assign the node control and financial benefit to different roles in order to provide more flexibilities for potential Filecoin financial market.

## Economics
### 3.1 Borrowing Interest Rate
#### 3.1.1	Design Rationale
A smart contract in the protocol updates and implements the borrowing interest rate with corresponding utilization rate. Utilization rate is the total amount of FIL borrowed in a share of total liquidity in the FIL lending pool at a certain point of time. The relationship between the borrowing interest rates and the utilization rates can be defined in a mathematical formula. Several factors should be taken into consideration,

•	Borrowing interest rate is determined by the utilization rate as in nature, a higher utilization, or a higher demand relative to supply, points to a higher interest rate, which increases the cost of capital;

•	The marginal interest rate (slope) should be constantly changing to accommodate the change of demand in the protocol, and the concept of diminishing of marginal utility should be taken into consideration. For example, the slope of the curve could be increasing along with the growth of the utilization rate;

•	The interest rate curve should be dynamic and programmable by defining critical points such as the minimum, maximum and interest rate at an optimal utilization;

•	Certain mechanisms should be considered to stable the borrowing rate at inception and prevent the protocol from running out of liquidity in case surge of utilization.

#### 3.1.2	Model Construction
#### 3.1.3	Parameters and Simulations
#### 3.1.4	Borrowings and Repayments

### 3.2 Deposit Income Allocation
#### 3.2.2	Design Rationale
Lenders who provide liquidity to the FILL Liquidity Pool expect to receive compensations on their opportunity cost of capital in forms of either interest income or capital appreciation. An automated system is designed to facilitate the distribution of the deposit benefit and determine the rate of return. Key factors of the system should include but not limited to,

•	Certificate tokens FLE are minted and granted to lenders who deposited FIL to the liquidity pool. FLE is acting as a proof of deposit and a vehicle of return allocation;

•	Deposit and withdraw is always open and based on the spot exchange rate among FLE and FIL;

•	The value of FLE in terms of FIL should constantly grow naturally as more FIL being borrowed and returned along with interest payment to the liquidity pool. Part of the interest paid by borrowers, is reflected in the value of the FLE token and transferred to the lenders;

•	For deposit, FLE/FIL exchange rate should be determined by utilization rate. Higher utilization rate makes it more cost-effective to deposit. As a protection to the system, the endpoint utilization rate should be applied for determination. 

•	For redemption, similarly, the amount of FIL can be redeemed with one unit of FLE should also be determined by the endpoint utilization. Higher utilization rate should attribute to less valuable FLE in terms of FIL, in other words, less cost-effective to redeem. 

•	However, the endpoint utilization rate cannot be directly determined at redemption. The system should apply the current exchange rate to calculate the approximated FIL to be redeemed and compare with the FIL available in the pool.

#### 3.2.1	Providing and Withdrawing FIL Liquidity
#### 3.2.3	Model Construction
#### 3.2.4	Parameters and Simulations
