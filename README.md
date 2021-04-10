# Database Design

![alt text](https://i.ibb.co/jghdRkk/Gumroad-Q3-DB-Design-6.png)

### Database Design Reasoning
1. We apply indexes on each of the tables' primary key and foreign key to help us reach the faster query runtime results


2. Index on `products` Name to help buyers to search product by name


3. Index of `purchases` `timestamp` to fetch all the purchases that happens between
a particular date range or happens up to a certain date. 
   

4. The `price_amount` column of a single `purchase` is necessary in case there is a product price changes in the future.
If we don't provide this, when there's a refund scenario after the `product` `price` has been changed,
   then the refunded amount might not be paid correctly.


5. Index on `payment_status` so later on we can fetch all the `PENDING` payments for rolling payout scenario
   

### Logic 
1. Purchases / Refunds : 
    
    - Purchase : 
        - When `buyers` made a `purchase`, add new `purchase` to the `purchase` table
        - Fetch `product` `price` to get the `price` of the purchased `product`   
        - Add the `sellers`' `balance` with the `price_amount`
        - Set the `payment_status` on `purchase` table to `PENDING`
        - Set the `payment_amount` on `purchase` table with the `price_amount`.
        - Deduct the payment to `buyer`'s `credit_card_number`
    
    - Refund :
        - When `seller` wants to refund a `purchase`, search on `purchase` table on which `purchase` they want to refund
        - Decrease the `seller`'s `balance` by the `purchase_amount`
        - Set the `purchase` status to `REFUNDED`
    
    - Rolling Payouts
        - Query on `purchase` table which has the `timestamps` value occurs before specific time (say, a week before) 
          with the `payment_status` is equal to `PENDING`
        - Transfer to the respective `seller`'s `bank_account_number` from `bank accounts` table
        - Change all the `payment_status` to `PAID`