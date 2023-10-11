
# Post \#69715819 [Link](https://stackoverflow.com/questions/69715819/)

## using ant design switch inside Forms

**Vote**: 2 (631/702) **Views**: 4284 (538/702) 

**Internal ID** \#0-0-14

Created at 2021-10-25 23:28:09

Tags: `reactjs` `antd`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

What's the correct way to use ant design switch inside, I could not get much from the official documentation.
[Switch-Ant Design](https://ant.design/components/switch/)
Here's how I am using it.
```
<Form form={form} layout="vertical">
  <Form.Item
    label="Description"
    name="description"
    rules={[{ required: true, message: 'Enter a description' }]}
  >
    <Input placeholder="Enter Description" />
  </Form.Item>

  <Form.Item name="switch" noStyle valuePropName="checked">
    <Switch checkedChildren="Yes" unCheckedChildren="No" />
    <span>Chargable</span>
  </Form.Item>

  <Button
    onClick={() => {
      form
        .validateFields()
        .then((values) => {
          form.resetFields()
          onCreate(values)
        })
        .catch((info) => {
          console.log('Validate Failed:', info)
        })
    }}
  >
    Save
  </Button>
</Form>
```

onCreate does no take the value from the switch, It does take it from the description
```
const onCreate = (values) => {}
```



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-10-26 04:37:45

------------

I was able to fix it but doing the following.
```
<td>
  <Form.Item valuePropName="checked" name="status" noStyle>
    <Switch checkedChildren="Yes" unCheckedChildren="No" />
  </Form.Item>
  <span className="ml-2">Status Enabled</span>
</td>
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-10-26 02:50:33

------------

I guess your values are `{description: "foo", switch: undefined}`?
In my demo, [switch demo](https://codesandbox.io/s/xiao-yan-qi-ta-zu-jian-antd-4-17-0-alpha-7-forked-nhxt7?file=/index.js), I add initialValue to Switch, so when I get values from the form, I get `{description: "111", switch: true}`.
I don't know whether this is what your mean.

---


or you can use like this
```
<Form.Item label="foo">         
  <Form.Item name="bar">           
    <Switch />         
  </Form.Item>         
  <span className="ant-form-text">Some text you want</span>       

</Form.Item>
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-02-08 11:14:01

------------

This is because you must only have one child inside a `Form.Item` component. Remove the `span` besides your Switch component and it will work.


------------
    
    