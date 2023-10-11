
# Post \#55620562 [Link](https://stackoverflow.com/questions/55620562/)

## ESLint: Component definition is missing displayName (react/display-name)

**Vote**: 36 (170/702) **Views**: 63006 (136/702) 

**Internal ID** \#0-0-26

Created at 2019-04-10 20:08:03

Tags: `javascript` `reactjs` `antd` `react-hooks`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm using a react hook component with antd. When setting up columns for a table, the render function is giving me an ESLint error:
> ESLint: Component definition is missing displayName
(react/display-name)
I've tried adding displayName to the object but this doesn't work.
This is how the error looks:
[](https://i.stack.imgur.com/dffZS.png)
This is the code:
```
const columns_payment_summary_table = [ 
    {
      title: FooConstants.LABEL_QUANTITY_SELECTED,
      dataIndex: 'group',
      key: 'group',
      render: text => (
        <span>{getCountForCountry(text)}</span>
      ),
    }
  ]
```

Can anyone help?
Here is full component code (well just the relevant bits)
```
import * as FooConstants from './constants'
import {connect} from 'react-redux'
import React, {useState, useEffect} from 'react'
import {Card, Table} from 'antd'
import PropTypes from 'prop-types'

const propTypes = {
  foos: PropTypes.object.isRequired,
}

function Foos(props) {

  const [selectedFooRows, setSelectedFooRows] = useState([])

  useEffect(() => {
    getFooDetails()
  }, [])

  function getFooDetails() {
    props.dispatch({
      type: FooConstants.GET_FOO_PAYMENT_SUMMARIES,
      params: {
        'group_by': 'country_code',
        'type': FooConstants.CLAIM_FOO,
      }
    })
    props.dispatch({
      type: FooConstants.GET_FOO_PAYMENTS,
      params: {'type': FooConstants.CLAIM_FOO, }
    })
  }

  const columns_payment_summary_table = [
    {
      title: FooConstants.LABEL_QUANTITY_SELECTED,
      dataIndex: 'group',
      key: 'group',
      render: text => (
        <span>{getCountForCountry(text)}</span>
      ),
    }
  ]

  function getCountForCountry(country_code){
    let selected_country = selectedFooRows.filter(function(row){
      return row.group === country_code
    })

    if(selected_country && selected_country.length > 0){
      return selected_country[0].ids.length
    } else {
      return 0
    }
  }

  return (
    <div>
      <Card
        title={FooConstants.LABEL_FOO_SUMMARY}>
        <Table
          columns={columns_payment_summary_table}
          bordered={true}
          dataSource={props.foos.foo_payment_summaries}
          loading={props.foos.foo_payment_summaries_pending && !props.foos.foo_payment_summaries}
          rowKey={record => record.group}
        />
      </Card>
    </div>
  )
}

Foos.propTypes = propTypes

const mapStateToProps = (state) => {
  return {foos: state.foosReducer}
}

export default connect(
  mapStateToProps,
)(Foos)
```



----------
        
## Answer \#0

**Accepted** Vote: 53

Created at 2019-04-10 20:36:05

------------

ESLint thinks you are defining a new component without setting any name to it.
This is explained because ESLint cannot recognize the [render prop pattern](https://reactjs.org/docs/render-props.html) because you are not directly writing this render prop into a component, but into an object.
You can either put the `render` prop directly into your jsx implementation of the `<Column>` component,
```
const columns_payment_summary_table_render = text => (<span>{getCountForCountry(text)}</span>);
 const columns_payment_summary_table = [{
    title: SettlementConstants.LABEL_QUANTITY_SELECTED, 
    dataIndex: "group", 
    key: "group",        
    // eslint-disable-next-line react/display-name        
    render: columns_payment_summary_table_render
 }];
```

or shut down the ESLint's error by doing this :
```
const columns_payment_summary_table = [ 
    {
        title: SettlementConstants.LABEL_QUANTITY_SELECTED,
        dataIndex: 'group',
        key: 'group',
        // eslint-disable-next-line react/display-name
        render: text => (
            <span>{getCountForCountry(text)}</span>
        ),
    }
]
```

I hope it helped ;)


------------
    
    
## Answer \#1

 Vote: 33

Created at 2021-06-13 08:45:16

------------

If anyone needs to avoid this in all the files, add below to the rules section of `.eslintrc.js` file,
```
{
  ...
  "rules": {
    "react/display-name": "off"
  }
}
```



------------
    
    
## Answer \#2

 Vote: 28

Created at 2020-01-07 17:29:43

------------

Using a normal function for the `render` key will also remove the ESLint warning without any need for disabling the warning.

```
const columns_payment_summary_table = [ 
    {
        title: SettlementConstants.LABEL_QUANTITY_SELECTED,
        dataIndex: 'group',
        key: 'group',
        render: function countForCountry(text) {
            return <span>{getCountForCountry(text)}</span>
        },
    }
]
```



------------
    
    
## Answer \#3

 Vote: 7

Created at 2020-09-02 15:22:09

------------

Sometimes we can bypass rules if we have an error in only one or two places. What if we have the same use case in multiple places. Each time We have to disable rules.
Instead, we can bypass this error by assigning function to render property.
```
const getMyHTML = (text) => <span>{getCountForCountry(text)}</span>

const columns_payment_summary_table = [
  {
    title: SettlementConstants.LABEL_QUANTITY_SELECTED,
    dataIndex: 'group',
    key: 'group',
    render: getMyHTML,
  }
]
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-07-20 15:30:20

------------

Using anonymous functions and arrow functions will keep `ESLint: Component definition is missing displayName (react/display-name)` coming out, I guess this is because that when we are rendering a component through a function, we are giving them a displayName by naming the render function as well.
But using the normal function isn't enough, you might still meet the following warning:
```
Warning: Functions are not valid as a React child. This may happen if you return a Component instead of <Component /> from render. Or maybe you meant to call this function rather than return it.
```

You have to execute the normal function just right after render it. I passed the two rules by using the following code.
```
render: () => {
    return (function Actions() {
        return (
            <Button>
            View
            </Button>
                );
            })();
        },
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2020-07-30 07:11:12

------------

This is covered pretty thoroughly in [this ESLint issue](https://github.com/yannickcr/eslint-plugin-react/issues/597).
As [Loïc](https://stackoverflow.com/users/4176917/lo%c3%afc-goyet) [suggested](https://stackoverflow.com/a/55620959/222356), suppressing the lint error is the simplest option here.
However, the lint error exists for a reason -- [displayName can be helpful for debugging](https://stackoverflow.com/questions/41581130/what-is-react-component-displayname-is-used-for), particularly in [React DevTools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)' Component view. The easiest way to assign your function a `displayName` is to use a named function declaration, and hoist the definition:
```
function renderTable(text) {
  return (<span>{getCountForCountry(text)}</span>);
}
const columns_payment_summary_table = [
  {
    title: SettlementConstants.LABEL_QUANTITY_SELECTED,
    dataIndex: 'group',
    key: 'group',
    render: text => (
      <span>{getCountForCountry(text)}</span>
    ),
  }
]
```

That's obviously more verbose, though, and if you don't anticipate needing to find the rendered component by name in DevTools, it's simplest to just suppress the error.


------------
    
    