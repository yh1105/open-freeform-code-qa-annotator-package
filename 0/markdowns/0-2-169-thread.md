
# Post \#61630846 [Link](https://stackoverflow.com/questions/61630846/)

## XSRF check failed when sending POST to custom rest endpoint

**Vote**: 3 (575/702) **Views**: 8220 (459/702) 

**Internal ID** \#0-2-169

Created at 2020-05-06 08:37:28

Tags: `html` `forms` `endpoint` `jira-plugin` `x-xsrf-token`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I have a ScriptRunner Fragment which shows a form dialog. Here is the code:

```
import com.onresolve.scriptrunner.runner.rest.common.CustomEndpointDelegate
import groovy.transform.BaseScript

import javax.ws.rs.core.MediaType
import javax.ws.rs.core.MultivaluedMap
import javax.ws.rs.core.Response

@BaseScript CustomEndpointDelegate delegate

showCloneEazyBIAccounts() { MultivaluedMap queryParams ->

def dialog =
    """<section role="dialog" id="sr-dialog" class="aui-layer aui-dialog2 aui-dialog2-medium" aria-hidden="true" data-aui-remove-on-hide="true">
        <header class="aui-dialog2-header">
            <h2 class="aui-dialog2-header-main">Clone EazyBI Accounts by Model</h2>
            <a class="aui-dialog2-header-close">
                <span class="aui-icon aui-icon-small aui-iconfont-close-dialog">Close</span>
            </a>
        </header>
        <div class="aui-dialog2-content">
            <form class="aui" action="/rest/scriptrunner/latest/custom/cloneJE2Cube" method="post">
                <div class="field-group">
                    <label for="accountNames">Account Names <span class="aui-icon icon-required"></span></label>
                    <input class="text medium-field" type="text"id="accountNames" name="accountNames" placeholder="Cubo 1, Cubo 2...">
                </div>
                <div class="field-group">
                    <label for="projectKeys">Project Keys <span class="aui-icon icon-required"></span></label>
                    <input class="text medium-field" type="text"id="projectKeys" name="projectKeys" placeholder="JESC, JEBACK....">
                </div>
                <div class="field-group">
                    <label for="model">Model <span class="aui-icon icon-required"></span></label>
                    <select class="select" id="model" name="model">
                        <option>Select</option>
                        <option>JESC</option>
                        <option>JEBACK</option>
                        <option>COM</option>
                        <option>AGILE</option>
                    </select>
                </div>
                <div class="buttons-container">
                    <div class="buttons">
                        <input class="button submit" type="submit" value="Clone" id="clone-button">
                    </div>
                </div>
            </form>
        </div>
        <footer class="aui-dialog2-footer">
             <div class="aui-dialog2-footer-hint"></div>
        </footer>
    </section>
    """

Response.ok().type(MediaType.TEXT_HTML).entity(dialog.toString()).build()
}
```


I need this form to send the data to another custom rest endpoint (that it works fine when i call it from postman), but when I submit this dialog, it appears "XSRF check failed".

Is there a way to make it work?

Best regards,
Eloi.


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-05-07 14:43:15

------------

Finally I solved the problem sending the request with AJAX and adding headers: { 'X-Atlassian-Token': 'nocheck' }.

Hope this helps!


------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-05-06 10:05:21

------------

It's because of headers that are added by your browser, In this case I think the problem is  header, override it to something dummy if it doen't solve the problem open networking tool of your browser and make override all custom headers which are added by your browser.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-08-17 10:02:12

------------

In our case, there were two possible solutions. 
- Change User Agents Header (Not possible as far as I know in Chrome)- Whitelist/Allowlist the origin domain in Jira. When you send a request to the Jira API your browser automatically populates the "origin" header. You need to add that value to the whitelist/allowlist in Jira. -> See here:
[https://confluence.atlassian.com/adminjiraserver073/configuring-the-allowlist-1014667631.html](https://confluence.atlassian.com/adminjiraserver073/configuring-the-allowlist-1014667631.html)
Type "Domain" should work.
See the issue documented by Atlassian:
[https://confluence.atlassian.com/jirakb/rest-api-calls-with-a-browser-user-agent-header-may-fail-csrf-checks-802591455.html](https://confluence.atlassian.com/jirakb/rest-api-calls-with-a-browser-user-agent-header-may-fail-csrf-checks-802591455.html)


------------
    
    