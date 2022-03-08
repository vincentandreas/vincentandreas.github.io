## Point Of Sales (POS) Application based on Node.js and Flutter

A few months ago, I and my friend making apps for stock inventory and record the purchase. The client needed mobile apps to be used by the cashier, and stock keeper in the store. For the division of task, my main task is to handle the backend apps, and my friend's task is making the mobile apps. The deadline of the task only 2 months, pretty challenging for us, that also work full time. In this article, I mainly talk about the backend side, not the mobile-apps. 

For the apps, We develop some features, such as: 

1. Stock inventory / Move Product feature
   <p>To monitoring product stock, from Warehouse to another Warehouse, Warehouse to Store, and vice versa.
2. Create Purchase feature
   <p>To record purchase bought by customer.
3. Financial Report feature
   <p>To show the sales report, from each Store, daily, monthly, or yearly. 
4. Related supporting features:
   <p>Such as: Create & manipulating product, customers, users, store, etc.


### Technology:

1. Backend-side :
   <p>For the backend technology, we using Node.js, with MySql database. To make sure the backend-apps run correctly, we also making end-to-end test using Mocha & Chai. We also use Winston to log some exception that might happen in the future. 
2. Mobile apps-side :
   <p>We use flutter, with BLoC pattern, to make more convinient and responseful state-change.
   
### Problem that we faced (Backend side):   

Of course, we find some problem while developing this apps. Here are some problems that we faced:

1. Make the Report feature can run lightly.
2. Carefully handle product stock that can be changed where Purchase detail updated by users or returned by customers.
3. Tricky part in ```new Date()``` in javascript. 
4. Tricky part in ```conn.beginTransaction()``` mysql.
5. Tricky part when getting mysql connection manually.
   
### Solution

1. For the report, rather than making the scheduler. I choose to using 3 tables, daily_sales, monthly_sales, and yearly_sales, for record the total sales, made by specified store in some period of time. When a request to check past daily sales, if the date already passed maximum days to update purchase, we can assume the purchase total already fixed, so we can save the total result to the daily_sales table. So if we want to access the same date later, the total can be obtain from the table. It also help when the user want to check monthly / yearly report. If some of the date already calculated before, the apps can directly

2. The purchase / invoice can be updated, if the user found some mistake in the invoice that need to be edited. The customer also can return some product in the invoice, if have two condition: 
   - The product is broken
   - The product is still good (returned by customer if the customer feel not really match with the product).
   <p>If the returned product's condition still good, we will add the product to the store's stock, but if the product's condition is pretty bad, the product can't be add to the store's stock.

3. Yes, the ```new Date()``` could return different date time, when you try it in local computer / server, even the server timezone already same. To handle that, one of my friend suggest to use Moment.js, to generate the datetime that we need. And it's really useful.
   
4. Be aware when using ```conn.beginTransaction()```. If you need to do another query inside the ```conn.beginTransaction()``` , you need to use same connection, as ```beginTransaction()``` use. If you using another connection, the result could be different.

   For example: If the product A stock is 10, then a customer buy 3pcs of product A, when you already done the process of substract the stock, but haven't commited yet, you can get different result, because of using different connection. 

   ``` javascript
   try{
      let conn = await getConnectionPromise();
      conn.beginTransaction()

      await updateStoreStock(conn, "product A", -3); // the stock reduce to 7

      // wrong way
      let newConn = await getConnectionPromise();
      await checkStock(newConn, "product A");  // the result still 10, because the transaction haven't been commited.

      // correct way
      await checkStock(conn, "product A"); // the result will 7

      await conn.commit();
   }
   catch(err) {
      await conn.rollback()
   }

   ``` 
5. If you get the connection manually from the pool, you need to release it again after the use, Otherwise, the pool will soon be empty of connections and you'll have a bunch of idle connections that can't be used by anyone. To solve that, you need to release it, after the connection finish, by  using ```conn.release()```

### Test

For the testing, I use Mocha and Chai, to do End to end (e2e) testing. We choose using e2e test to make sure another process related to the test also processed. For example, if the purchase occured, we want to make sure, the product stock also decreased, fit with product that bought by customer. We can make whole scenario, from new store created, stock being added to the store, create new customer, and the new customer buy the product. This is the example of e2e test when adding new product stock:

``` javascript
it("Make new PROD_ID stock to 50", function (done) {
        chai.request(TEST_URL)
        .get(`/api/storages?name=${TEST_DATA_OBJ.STORE_NAME}&type=store`)
        .set('Authorization', AUTH_STR)
        .send()
        .end(function (err, res) {
            let obj = JSON.parse(res.text);
            idStok = "" + obj[0].id;

            chai.request(TEST_URL)
                .post('/api/storageStocks')
                .set('Authorization', AUTH_STR)
                .send({
                    "storageId": idStok,
                    "productId": TEST_DATA_OBJ.PROD_ID,
                    "qty": "50"
                })
                .end(function (err, res) {
                    let textmsg = `{"message":"${RESP_MSG_OBJ.STORAGE_STOCK_SCS}"}`;
                    chai.expect(res.text).to.equal(textmsg);
                    done();
                });
        });

    });


```
### Apps view
1. Main Menu
![Main menu](./images/mainmenu.png)   
2. New Product page
![New Product page](./images/newprod.png)
3. Add stock page
![Add stock page](./images/addstock.png)
4. View product stock page
![View product page](./images/view-prod-stock.png)
5. View store stock page
![View store page](./images/view-store-stock.png)
6. Purchase page
![Purchase page](./images/purchase.png)
7. Invoice view
![Invoice page](./images/see-purchase.png)
8. Report page
![Report page](./images/report.png)

