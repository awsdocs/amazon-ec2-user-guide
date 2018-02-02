# Selling on the Reserved Instance Marketplace<a name="ri-market-general"></a>

The Reserved Instance Marketplace is a platform that supports the sale of third\-party and AWS customers' unused Standard Reserved Instances, which vary in term lengths and pricing options\. For example, you may want to sell Reserved Instances after moving instances to a new AWS region, changing to a new instance type, ending projects before the term expiration, when your business needs change, or if you have unneeded capacity\.

If you want to sell your unused Reserved Instances on the Reserved Instance Marketplace, you must meet certain eligibility criteria\.


+ [Selling in the Reserved Instance Marketplace](#ri-market-selling-guide)
+ [Buying in the Reserved Instance Marketplace](#ri-market-buying-guide)

## Selling in the Reserved Instance Marketplace<a name="ri-market-selling-guide"></a>

As soon as you list your Reserved Instances in the Reserved Instance Marketplace, they are available for potential buyers to find\. All Reserved Instances are grouped according to the duration of the term remaining and the hourly price\.

To fulfill a buyer's request, AWS first sells the Reserved Instance with the lowest upfront price in the specified grouping\. Then, we sell the Reserved Instance with the next lowest price, until the buyer's entire order is fulfilled\. AWS then processes the transactions and transfers ownership of the Reserved Instances to the buyer\. 

You own your Reserved Instance until it's sold\. After the sale, you've given up the capacity reservation and the discounted recurring fees\. If you continue to use your instance, AWS charges you the On\-Demand price starting from the time that your Reserved Instance was sold\.


+ [Restrictions and Limitations](#ri-seller-limits)
+ [Registering as a Seller](#ri-market-seller-profile)
+ [Pricing Your Reserved Instances](#ri-market-concepts-pricing)
+ [Listing Your Reserved Instances](#ri-market-selling-listing)
+ [Lifecycle of a Listing](#ri-market-concepts-sold-partial)
+ [After Your Reserved Instance Is Sold](#ri-market-concepts-sold)

### Restrictions and Limitations<a name="ri-seller-limits"></a>

Before you can sell your unused reservations, you must register as a seller in the Reserved Instance Marketplace\. For information, see [Registering as a Seller](#ri-market-seller-profile)\.

The following limitations and restrictions apply when selling Reserved Instances:

+ Only Amazon EC2 Standard Reserved Instances can be sold in the Reserved Instance Marketplace\. Convertible Reserved Instances cannot be sold\.

+ The minimum price allowed in the Reserved Instance Marketplace is $0\.00\.

+ Reserved Instances can be sold only after AWS has received the upfront payment and the reservation has been active \(you've owned it\) for at least 30 days\. In addition, there must be at least one month remaining in the term of the Standard Reserved Instance you are listing\.

+ You cannot modify your listing in the Reserved Instance Marketplace directly\. However, you can change your listing by first canceling it and then creating another listing with new parameters\. For information, see [Pricing Your Reserved Instances](#ri-market-concepts-pricing)\. You can also modify your Reserved Instances before listing them\. For information, see [Modifying Reserved Instances](ri-modifying.md)\.

+ AWS charges a service fee of 12 percent of the total upfront price of each Standard Reserved Instance you sell in the Reserved Instance Marketplace\. The upfront price is the price the seller is charging for the Standard Reserved Instance\.

+ Only Amazon EC2 Standard Reserved Instances can be sold in the Reserved Instance Marketplace\. Other AWS Reserved Instances, such as Amazon RDS and Amazon ElastiCache Reserved Instances cannot be sold in the Reserved Instance Marketplace\.

### Registering as a Seller<a name="ri-market-seller-profile"></a>

To sell in the Reserved Instance Marketplace, you must first register as a seller\. During registration, you provide the following information:

+ **Bank information**—AWS must have your bank information in order to disburse funds collected when you sell your reservations\. The bank you specify must have a US address\. For more information, see [Bank Accounts](#ri-market-concepts-bank)\.

+ **Tax information**—Sellers who have 50 or more transactions or who plan to sell $20,000 or more in Standard Reserved Instances have to provide additional information about their business for tax reasons\. For information, see [Tax Information](#ri-market-concepts-taxinfo)\.

After AWS receives your completed seller registration, you receive an email confirming your registration and informing you that you can get started selling in the Reserved Instance Marketplace\.


+ [Bank Accounts](#ri-market-concepts-bank)
+ [Tax Information](#ri-market-concepts-taxinfo)
+ [Sharing Information with the Buyer](#ri-market-seller-disclosure)
+ [Getting Paid](#ri-market-sold-gettingpaid)

#### Bank Accounts<a name="ri-market-concepts-bank"></a>

AWS must have your bank information in order to disburse funds collected when you sell your Reserved Instance\. The bank you specify must have a US address\.

**To register a default bank account for disbursements**

1. Open the [Reserved Instance Marketplace Seller Registration](https://portal.aws.amazon.com/ec2/ri/seller_registration) page and sign in using your AWS credentials\.

1. On the **Manage Bank Account** page, provide the following information about the bank through to receive payment:

   + Bank account holder name

   + Routing number

   + Account number

   + Bank account type
**Note**  
If you are using a corporate bank account, you are prompted to send the information about the bank account via fax \(1\-206\-765\-3424\)\.

After registration, the bank account provided is set as the default, pending verification with the bank\. It can take up to two weeks to verify a new bank account, during which time you can't receive disbursements\. For an established account, it usually takes about two days for disbursements to complete\.

**To change the default bank account for disbursement**

1. On the [Reserved Instance Marketplace Seller Registration](https://portal.aws.amazon.com/ec2/ri/seller_registration) page, sign in with the account that you used when you registered\.

1. On the **Manage Bank Account** page, add a new bank account or modify the default bank account as needed\.

#### Tax Information<a name="ri-market-concepts-taxinfo"></a>

Your sale of Reserved Instances might be subject to a transactional tax, such as sales tax or value\-added tax\. You should check with your business's tax, legal, finance, or accounting department to determine if transaction\-based taxes are applicable\. You are responsible for collecting and sending the transaction\-based taxes to the appropriate tax authority\.

As part of the seller registration process, you have the option of completing a tax interview\. We encourage you to complete this process if any of the following apply:

+ You want AWS to generate a Form 1099\-K\.

+ You anticipate having either 50 or more transactions or $20,000 or more in sales of Reserved Instances in a calendar year\. A transaction can involve one or more Reserved Instances\. If you choose to skip this step during registration, and later you reach transaction 49, you get a message saying, "You have reached the transaction limit for pre\-tax\. Please complete the tax interview in the [Seller Registration Portal](https://portal.aws.amazon.com/ec2/ri/seller_registration?action=taxInterview)\." After the tax interview is completed, the account limit is automatically increased\.

+ You are a non\-US seller\. In this case, you must electronically complete Form W\-8BEN\.

For more information about IRS requirements and the Form 1099\-K, see the [IRS website](http://www.irs.gov/uac/FAQs-on-New-Payment-Card-Reporting-Requirements)\.

The tax information you enter as part of the tax interview differs depending on whether your business is a US or non\-US legal entity\. As you fill out the tax interview, keep in mind the following:

+ Information provided by AWS, including the information in this topic, does not constitute tax, legal, or other professional advice\. To find out how the IRS reporting requirements might affect your business, or if you have other questions, please contact your tax, legal, or other professional advisor\.

+ To fulfill the IRS reporting requirements as efficiently as possible, answer all questions and enter all information requested during the interview\.

+ Check your answers\. Avoid misspellings or entering incorrect tax identification numbers\. They can result in an invalidated tax form\. 

After you complete the tax registration process, AWS files Form 1099\-K\. You will receive a copy of it through the US mail on or before January 31 in the year following the year that your tax account reaches the threshold levels\. For example, if your tax account reaches the threshold in 2016, you receive the form in 2017\.

#### Sharing Information with the Buyer<a name="ri-market-seller-disclosure"></a>

When you sell in the Reserved Instance Marketplace, AWS shares your company’s legal name on the buyer’s statement in accordance with US regulations\. In addition, if the buyer calls AWS Support because the buyer needs to contact you for an invoice or for some other tax\-related reason, AWS may need to provide the buyer with your email address so that the buyer can contact you directly\.

For similar reasons, the buyer's ZIP code and country information are provided to the seller in the disbursement report\. As a seller, you might need this information to accompany any necessary transaction taxes that you remit to the government \(such as sales tax and value\-added tax\)\.

AWS cannot offer tax advice, but if your tax specialist determines that you need specific additional information, [contact AWS Support](https://aws.amazon.com//contact-us/)\.

#### Getting Paid<a name="ri-market-sold-gettingpaid"></a>

As soon as AWS receives funds from the buyer, a message is sent to the registered owner account email for the sold Reserved Instance\.

AWS sends an Automated Clearing House \(ACH\) wire transfer to your specified bank account\. Typically, this transfer occurs between one to three days after your Reserved Instance has been sold\. You can view the state of this disbursement by viewing your Reserved Instance disbursement report\. Disbursements take place once a day\. Keep in mind that you can't receive disbursements until AWS has received verification from your bank\. This period can take up to two weeks\.

The Reserved Instance that you sold continues to appear when you describe your Reserved Instances\.

You receive a cash disbursement for your Reserved Instances through a wire transfer directly into your bank account\. AWS charges a service fee of 12 percent of the total upfront price of each Reserved Instance you sell in the Reserved Instance Marketplace\.

### Pricing Your Reserved Instances<a name="ri-market-concepts-pricing"></a>

The upfront fee is the only fee that you can specify for the Reserved Instance that you're selling\. The upfront fee is the one\-time fee that the buyer pays when they purchase a Reserved Instance\. You cannot specify the usage fee or the recurring fee; The buyer pays the same usage or recurring fees that were set when the reservations were originally purchased\.

The following are important limits to note:

+ **You can sell up to $50,000 in Reserved Instances per year**\. To sell more, complete the [Request to Raise Sales Limit on Amazon EC2 Reserved Instances](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-reserved-instance-sales) form\.

+ **The minimum price is $0**\. The minimum allowed price in the Reserved Instance Marketplace is $0\.00\.

You cannot modify your listing directly\. However, you can change your listing by first canceling it and then creating another listing with new parameters\.

You can cancel your listing at any time, as long as it's in the `active`state\. You cannot cancel the listing if it's already matched or being processed for a sale\. If some of the instances in your listing are matched and you cancel the listing, only the remaining unmatched instances are removed from the listing\.

#### Setting a Pricing Schedule<a name="pricing-schedule"></a>

Because the value of Reserved Instances decreases over time, by default, AWS can set prices to decrease in equal increments month over month\. However, you can set different upfront prices based on when your reservation sells\.

For example, if your Reserved Instance has nine months of its term remaining, you can specify the amount that you would accept if a customer were to purchase that Reserved Instance with nine months remaining\. You could set another price with five months remaining, and yet another price with one month remaining\.

### Listing Your Reserved Instances<a name="ri-market-selling-listing"></a>

As a registered seller, you can choose to sell one or more of your Reserved Instances\. You can choose to sell all of them in one listing or in portions\. In addition, you can list Reserved Instances with any configuration of instance type, platform, and scope\.

If you cancel your listing and a portion of that listing has already been sold, the cancellation is not effective on the portion that has been sold\. Only the unsold portion of the listing is no longer available in the Reserved Instance Marketplace\.


+ [Listing Your Reserved Instance Using the AWS Management Console](#ri-market-concepts-listing)
+ [Listing Your Reserved Instances Using the AWS CLI or Amazon EC2 API](#listing-ri-cli)
+ [Reserved Instance Listing States](#ri-listing-states)

#### Listing Your Reserved Instance Using the AWS Management Console<a name="ri-market-concepts-listing"></a>

**To list a Reserved Instance in the Reserved Instance Marketplace using the AWS Management Console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Select the Reserved Instances to list, and choose **Sell Reserved Instances**\.

1. On the **Configure Your Reserved Instance Listing** page, set the number of instances to sell and the upfront price for the remaining term in the relevant columns\. See how the value of your reservation changes over the remainder of the term by selecting the arrow next to the **Months Remaining** column\.

1. If you are an advanced user and you want to customize the pricing, you can enter different values for the subsequent months\. To return to the default linear price drop, choose **Reset**\.

1. Choose **Continue** when you are finished configuring your listing\.

1. Confirm the details of your listing, on the **Confirm Your Reserved Instance Listing** page and if you're satisfied, choose **List Reserved Instance**\.

**To view your listings in the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Select the Reserved Instance that you've listed and choose **My Listings**\.

#### Listing Your Reserved Instances Using the AWS CLI or Amazon EC2 API<a name="listing-ri-cli"></a>

**To list a Reserved Instance in the Reserved Instance Marketplace using the AWS CLI**

1. Get a list of your Reserved Instances by using the [describe\-reserved\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) command\.

1. Note the ID of the Reserved Instance you want to list and call [create\-reserved\-instances\-listing](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-reserved-instances-listing.html)\. You must specify the ID of the Reserved Instance, the number of instances, and the pricing schedule\.

1. To view your listing, use the [describe\-reserved\-instances\-listings](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-listings.html) command\.

To cancel your listing, use the [cancel\-reserved\-instances\-listings](http://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-reserved-instances-listings.html) command\.

**To list a Reserved Instance in the Reserved Instance Marketplace using the Amazon EC2 API**

+ [DescribeReservedInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeReservedInstances.html)

+ [CreateReservedInstancesListing](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateReservedInstancesListing.html)

+ [DescribeReservedInstancesListings](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeReservedInstancesListings.html)

+ [CancelReservedInstancesListing](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CancelReservedInstancesListing.html)

#### Reserved Instance Listing States<a name="ri-listing-states"></a>

**Listing State** on the **My Listings** tab of the Reserved Instances page displays the current status of your listings:

The information displayed by **Listing State** is about the status of your listing in the Reserved Instance Marketplace\. It is different from the status information that is displayed by the **State** column in the **Reserved Instances** page\. This **State** information is about your reservation\.

+ **active**—The listing is available for purchase\.

+ **canceled**—The listing is canceled and isn't available for purchase in the Reserved Instance Marketplace\.

+ **closed**—The Reserved Instance is not listed\. A Reserved Instance might be `closed` because the sale of the listing was completed\.

### Lifecycle of a Listing<a name="ri-market-concepts-sold-partial"></a>

When all the instances in your listing are matched and sold, the **My Listings** tab shows that the **Total instance count** matches the count listed under **Sold**\. Also, there are no **Available** instances left for your listing, and its **Status** is `closed`\.

When only a portion of your listing is sold, AWS retires the Reserved Instances in the listing and creates the number of Reserved Instances equal to the Reserved Instances remaining in the count\. So, the listing ID and the listing that it represents, which now has fewer reservations for sale, is still active\.

Any future sales of Reserved Instances in this listing are processed this way\. When all the Reserved Instances in the listing are sold, AWS marks the listing as `closed`\.

For example, you create a listing *Reserved Instances listing ID 5ec28771\-05ff\-4b9b\-aa31\-9e57dexample* with a listing count of 5\.

The **My Listings** tab in the **Reserved Instance** console page displays the listing this way:

*Reserved Instance listing ID 5ec28771\-05ff\-4b9b\-aa31\-9e57dexample*

+ Total reservation count = 5

+ Sold = 0

+ Available = 5

+ Status = active

 A buyer purchases two of the reservations, which leaves a count of three reservations still available for sale\. Because of this partial sale, AWS creates a new reservation with a count of three to represent the remaining reservations that are still for sale\.

This is how your listing looks in the **My Listings** tab:

*Reserved Instance listing ID 5ec28771\-05ff\-4b9b\-aa31\-9e57dexample*

+ Total reservation count = 5

+ Sold = 2

+ Available = 3

+ Status = active

If you cancel your listing and a portion of that listing has already sold, the cancelation is not effective on the portion that has been sold\. Only the unsold portion of the listing is no longer available in the Reserved Instance Marketplace\.

### After Your Reserved Instance Is Sold<a name="ri-market-concepts-sold"></a>

When your Reserved Instance is sold, AWS sends you an email notification\. Each day that there is any kind of activity, you receive one email notification capturing all the activities of the day\. For example, you may create or sell a listing, or AWS may send funds to your account\. 

To track the status of a Reserved Instance listing in the console, choose **Reserved Instance**, **My Listings**\. The **My Listings** tab contains the **Listing State** value\. It also contains information about the term, listing price, and a breakdown of how many instances in the listing are available, pending, sold, and canceled\. You can also use the [describe\-reserved\-instances\-listings](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-listings.html) command with the appropriate filter to obtain information about your listings\.

## Buying in the Reserved Instance Marketplace<a name="ri-market-buying-guide"></a>

You can purchase Reserved Instances from third\-party sellers who own Reserved Instances that they no longer need from the Reserved Instance Marketplace\. You can do this using the Amazon EC2 console or a command line tool\. The process is similar to purchasing Reserved Instances from AWS\. For more information, see [Buying Reserved Instances](ri-market-concepts-buying.md)\.

There are a few differences between Reserved Instances purchased in the Reserved Instance Marketplace and Reserved Instances purchased directly from AWS:

+ **Term**—Reserved Instances that you purchase from third\-party sellers have less than a full standard term remaining\. Full standard terms from AWS run for one year or three years\.

+ **Upfront price**—Third\-party Reserved Instances can be sold at different upfront prices\. The usage or recurring fees remain the same as the fees set when the Reserved Instances were originally purchased from AWS\.

+ **Types of Reserved Instances**—Only Amazon EC2 Standard Reserved Instances can be purchased from the Reserved Instance Marketplace\. Convertible Reserved Instances, Amazon RDS and Amazon ElastiCache Reserved Instances are not available for purchase on the Reserved Instance Marketplace\.

Basic information about you is shared with the seller, for example, your ZIP code and country information\.

This information enables sellers to calculate any necessary transaction taxes that they have to remit to the government \(such as sales tax or value\-added tax\) and is provided as a disbursement report\. In rare circumstances, AWS might have to provide the seller with your email address, so that they can contact you regarding questions related to the sale \(for example, tax questions\)\.

For similar reasons, AWS shares the legal entity name of the seller on the buyer's purchase invoice\. If you need additional information about the seller for tax or related reasons, contact [AWS Support](https://aws.amazon.com//contact-us/)\.