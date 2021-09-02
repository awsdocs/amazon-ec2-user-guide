# Maximum price overrides<a name="ec2-fleet-price-overrides"></a>

Each EC2 Fleet can either include a global maximum price, or use the default \(the On\-Demand price\)\. The fleet uses this as the default maximum price for each of its launch specifications\.

You can optionally specify a maximum price in one or more launch specifications\. This price is specific to the launch specification\. If a launch specification includes a specific price, the EC2 Fleet uses this maximum price, overriding the global maximum price\. Any other launch specifications that do not include a specific maximum price still use the global maximum price\.