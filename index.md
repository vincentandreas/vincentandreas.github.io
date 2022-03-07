## Point Of Sales (POS) Application based on Node.js and Flutter

A few months ago, we were making apps for stock inventory and record the purchase. The client needed mobile apps based. 

We develop some feature, such as: 

1. Stock inventory / Move Product feature
   To monitoring product stock, from Warehouse to another Warehouse, or Warehouse to Store, and vice versa.
2. Create Purchase feature
   To record purchase.
3. Financial Report feature
   To show the sales report, from each Store, daily, monthly, or yearly. 
   
   
Technology:

1. Backend-side :
   For the backend technology, we using Express.js, with MySql database. To make sure the backend-apps run correctly, we also making end-to-end test using Mocha. We also use Winston to log some exception that might happen in the future. 
2. Mobile apps-side :
   We use flutter, with BLoC pattern, to make more convinient and responseful state-change.
   
### Problem that we faced:   

Of course, we find some problem while developing this apps, handled by 2 person that also have full time work. We only have 2 months for developing those apps. Here are some problems that we faced:

1. Make the Report feature can run as light as possible.
2. Carefully handle product stock that might be returned by the customer 

Solution

1. For the report, rather that making the scheduler. I choose to using 3 tables, daily_sales, monthly_sales, and yearly_sales, for record the total sales, made by specified store in some period of time. When a request to check past daily sales, if the date already passed maximum days to update purchase, we can assume the purchase total already fixed, so we can save the total result to the daily_sales table. So if we want to access the same date later, the total can be obtain from the table. It also help when the user want to check monthly / yearly report. If some of the date already calculated before, the apps can directly


### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image]('images/addstock.png')
```

<img src="{{site.baseurl | prepend: site.url}}images/addstock.png" alt="Untitled" />


For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/vincentandreas/vincentandreas.github.io/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
