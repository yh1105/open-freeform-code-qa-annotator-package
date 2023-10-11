
# Post \#70471199 [Link](https://stackoverflow.com/questions/70471199/)

## Click Button & filter data by specific character In jQuery

**Vote**: 0 (696/702) **Views**: 1149 (650/702) 

**Internal ID** \#2-7-429

Created at 2021-12-24 09:01:58

Tags: `javascript` `php` `html` `jquery` `wordpress`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `html` `javascript` `php`)

----------

**Notepad**


----------


My Dropdown is working fine but I want to filter the input field also.
As I add the Name  then I get the data.
If I filter full name then I get data but I want me to enter some name then I get filter data like:  etc.
```
    $(document).ready(function () {
        $(".sbtns").on("click", function () {
            var name = $('#snme').val();
            //$('.item div:contains('+name+')').addClass('color');
            var coundivy = $('#ddlCoundivy').find("option:selected").val();
            var age = $('#ddlAge').find("option:selected").val();
            SearchData(name, coundivy, age)
        });
    });
    function SearchData(name, coundivy, age) {
        if (name.toUpperCase() == '' && coundivy.toUpperCase() == 'ALL' && age.toUpperCase() == 'ALL') {
            $('#table11 .item').show();
        } else {
            $('#table11 .item:has(div)').each(function () {
                var rowName = $.trim($(this).find('div:eq(0)').text());
                //var findname = $('div:contains('+rowName+')').addClass('color');
                var rowCoundivy = $.trim($(this).find('div:eq(1)').text());
                var rowAge = $.trim($(this).find('div:eq(2)').text());
                if (name.toUpperCase() !='' && coundivy.toUpperCase() != 'ALL' && age.toUpperCase() != 'ALL') {
                    if (rowCoundivy.toUpperCase() == coundivy.toUpperCase() && rowAge == age && rowName == name) {
                        $(this).show();
                    } else {
                        $(this).hide();
                    }
                } else if ($(this).find('div:eq(1)').text() != '' || $(this).find('div:eq(1)').text() != '' || $(this).find('div:eq(1)').text() != '') {
                  if (name != '') {
                        if (rowName.toUpperCase() == name.toUpperCase()) {
                            $(this).show();
                        } else {
                            $(this).hide();
                        }
                    }
                   if (coundivy != 'all') {
                        if (rowCoundivy.toUpperCase() == coundivy.toUpperCase()) {
                            $(this).show();
                        } else {
                            $(this).hide();
                        }
                    }
                    if (age != 'all') {
                        if (rowAge == age) {
                            $(this).show();
                        }
                        else {
                            $(this).hide();
                        }
                    }
                }
 
            });
        }
    }
```
```
<input id="snme" name="sa_snme" value="" placeholder="Name?" type="text" class="s_snme" autocomplete="off">
<select class="cl_coundivy" id="ddlCoundivy">
    <option value="all">Select Class </option>
    <option value="India">india</option>
    <option value="usa">usa</option>
    <option value="uk">uk</option>
</select>
<select class="cl_age" id="ddlAge">
    <option value="all">Select Class </option>
    <option value="50">50</option>
    <option value="60">60</option>
    <option value="80">80</option>
</select>
<input type="button" value="" class="sbtns" name="search_submit" data-post="39" value="click">
<div style="width: 100%" id="table11" border="1">
    <div class="tr">
        <div>name</div>
        <div>coundivy</div>
        <div>Age</div>
    </div>
     <div class="item">
        <div>Jill name</div>
        <div>USA</div>
        <div>50</div>
    </div>
     <div class="item">
        <div>Eve hel</div>
        <div>UK</div>
        <div>50</div>
    </div>
     <div class="item">
        <div>John martin</div>
        <div>INDIA</div>
        <div>80</div>
    </div>
     <div class="item">
        <div>sam karan</div>
        <div>AUSdivALIA</div>
        <div>80</div>
    </div>
     <div class="item">
        <div>joe khan</div>
        <div>INDIA</div>
        <div>60</div>
    </div>
     <div class="item">
        <div>alan walker</div>
        <div>USA</div>
        <div>60</div>
    </div>
</div>
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
```


Can you guys help me i will be very happy



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-12-24 14:36:17

------------

@dk-thakur, you can replace your SearchData() function with the below code. It will work for all conditions
```
function SearchData(name, coundivy, age) {
        if (
          name.toUpperCase() == "" &&
          coundivy.toUpperCase() == "ALL" &&
          age.toUpperCase() == "ALL"
        ) {
          $("#table11 .item").show();
        } else {
          $("#table11 .item:has(div)").each(function () {
            var rowName = $.trim($(this).find("div:eq(0)").text());
            var rowCoundivy = $.trim($(this).find("div:eq(1)").text());
            var rowAge = $.trim($(this).find("div:eq(2)").text());

            var condition = false;
            if (name != "") {
              var condition = rowName
                .toLowerCase()
                .includes(name.toLowerCase());
              if (coundivy != "all") {
                condition =
                  condition &&
                  rowCoundivy.toUpperCase() == coundivy.toUpperCase();
              }
              if (age != "all") {
                condition = condition && rowAge == age;
              }
            } else {
              if (coundivy != "all") {
                var condition =
                  rowCoundivy.toUpperCase() == coundivy.toUpperCase();
                if (age != "all") {
                  condition = condition && rowAge == age;
                }
              } else {
                if (age != "all") {
                  var condition = rowAge == age;
                }
              }
            }

            if (condition) {
              $(this).show();
            } else {
              $(this).hide();
            }
          });
          if ($("#table11").find("div.item:visible").length == 0) {
            $("#table11").append("<div id='no_data'>No Data Found!</div>");
          } else {
            $("#table11").find("div#no_data:visible").remove();
          }
        }
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-12-24 09:22:02

------------

try this code :
```
if (rowName.toLowerCase().search(name.toLowerCase()) != -1) {
    $(this).show();
   } else {
    $(this).hide();
}
```

other conditions
```
if (name.toUpperCase() !='' && coundivy.toUpperCase() != 'ALL' && age.toUpperCase() != 'ALL') {
  if (rowCoundivy.toUpperCase() == coundivy.toUpperCase() && rowAge == age && rowName.toLowerCase().search(name.toLowerCase()) != -1) {
     $(this).show();
  } else {
   $(this).hide();
  } }
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-12-24 11:13:55

------------

You can try this also
```
if (name.toUpperCase() !='' && coundivy.toUpperCase() != 'ALL' && age.toUpperCase() != 'ALL') {
  if (rowCoundivy.toUpperCase() == coundivy.toUpperCase() && rowAge == age && rowName.toLowerCase().includes(name.toLowerCase())) {
     $(this).show();
  } else {
   $(this).hide();
  } }
```



------------
    
    