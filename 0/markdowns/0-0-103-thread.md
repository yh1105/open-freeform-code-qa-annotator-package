
# Post \#61535210 [Link](https://stackoverflow.com/questions/61535210/)

## How to format date in ant table?

**Vote**: 1 (672/702) **Views**: 9244 (427/702) 

**Internal ID** \#0-0-103

Created at 2020-05-01 00:49:35

Tags: `javascript` `datatable` `antd`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a table with 6 columns, one of these columns is a date, The default display format of the date is `2020-04-30T21:30:07.000Z` I want to change it to `30-04-2020`.

```
export const columns = [
    { title: `ID`, dataIndex: `invoice_id`},
    { title: `Invoice Number`, dataIndex: `invoice_number` },
    { title: `Amount`, dataIndex: `invoice_amount`},
    { title: `Store Name`, dataIndex: `store_name`},
    { title: `Supplier Name`, dataIndex: `supplier_name`},
    { title: `Creation Date`, dataIndex: `invoice_date`,},
  ]

<Table bordered columns={columns} dataSource={invoices_list} />
```


I believe there should be a way to pass date format to the columns. I looked into the `ant` table documentations but didn't find a solution.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2020-05-01 01:18:09

------------

After looking into the antd documentation ([https://ant.design/components/table/#API](https://ant.design/components/table/#API)) it doesn't seems to have a property to handle your case. You should duplicate your column `invoices_date` to `invoices_date_printabble` which will have the good format to be printed.

```
invoices_list.map(el => {
    let date = new Date(el.invoices_date)
    el.invoices_date_printabble = date.getDay()+"/"+date.getMonth()+"/"+date.getFullYear()
})
```


And now your list is printable.

```
export const columns = [
    { title: `ID`, dataIndex: `invoice_id`},
    { title: `Invoice Number`, dataIndex: `invoice_number` },
    { title: `Amount`, dataIndex: `invoice_amount`},
    { title: `Store Name`, dataIndex: `store_name`},
    { title: `Supplier Name`, dataIndex: `supplier_name`},
    { title: `Creation Date`, dataIndex: `invoices_date_printabble`},
  ]

<Table bordered columns={columns} dataSource={invoices_list} />
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-03-03 08:58:09

------------

Another way to implement it is to define a 'render' for a date column like
```
{
 title: 'Published at',
 dataIndex: 'publishedAt',
 key: 'publishedAt',
 width: 210,
 render: ((date:string) => getFullDate(date)) }
```

and just utilize date formatting function getFullDate. In your case it can look like
```
export const getFullDate = (date: string): string => {
  const dateAndTime = date.split('T');

  return dateAndTime[0].split('-').reverse().join('-');
};
```

In this case you'll also avoid using external libraries and will have a function to format the date in other places.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-05-01 02:18:14

------------

Another way to do it using `moment.js` library.

First install moment.js into your project

```
npm i moment
```


Second import it to your component

```
import moment from 'moment';
```


Third change date format:

```
let invoices=response.data
invoices.map(el => {
    let date = moment(new Date(el.invoice_date));
    el.invoice_date = date.format("DD/MM/YYYY")
})
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-09-14 12:35:32

------------

this worked for me!
```
export const columns = [
      {
      title: "ID",
      key: "key",
      render: (record) => {
        return (
          <div> 
            <p>{moment(record.invoice_id).format("DD-MM-YYYY")}</p>
          </div>
        );
      },
    },
  ]
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-05-10 02:53:22

------------

I think the easiest way would be to use moment.js in the render method of the column options:

Your moment format of your existing date can be found in the moment documentation. 

For future reference you can always use a millisecond timestamp and convert to whatever date format you want with moment.  

```
export const columns = [
....
{ title: "Creation Date", 
dataIndex: "invoices_date",
render: (invoice_date) => { return (<p>{moment(invoices_date, **"Moment Format"**).format(DD-MM-YYYY)}</p>)},
```


]


------------
    
    