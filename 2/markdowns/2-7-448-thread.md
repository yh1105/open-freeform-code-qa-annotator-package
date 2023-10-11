
# Post \#71485050 [Link](https://stackoverflow.com/questions/71485050/)

## how to make a table scrollable with html + Tailwind CSS

**Vote**: 2 (631/702) **Views**: 10760 (408/702) 

**Internal ID** \#2-7-448

Created at 2022-03-15 15:44:58

Tags: `html` `css` `laravel` `tailwind-css`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `css` `html` `php`)

----------

**Notepad**


----------

i have this table like in the image below, it is overflow from the right side, how can I add scrolling, I am using Tailwind CSS like in this code:
```
<table class="table-auto overflow-scroll">
                    <thead>
                    <tr class="bg-gray-100">
                        <th class="w-20 px-4 py-2">No.</th>
                        <th class="px-4 py-2">First Name</th>
                        <th class="px-4 py-2">Second Name</th>
                        <th class="px-4 py-2">Third Name</th>
                        <th class="px-4 py-2">Department</th>
                        <th class="px-4 py-2">Stage</th>
                        <th class="px-4 py-2">Email</th>
                        <th class="px-4 py-2">Roles</th>
                        <th class="px-4 py-2">status</th>
                        <th class="px-4 py-2">University Email</th>
                        <th class="px-4 py-2">University Password</th>
                        <th class="px-4 py-2">Students Files</th>
                        <th class="px-4 py-2">Actions</th>
                    </tr>
                    </thead>
                    <tbody>

                        @if(isset($users)) @include('dashboard.users.partials.users_details') @endif
                        @if(isset($searches)) @include('dashboard.users.partials.search') @endif
                        @if(isset($statusSearch)) @include('dashboard.users.partials.status_search') @endif

                    </tbody>
                </table>
```

[](https://i.stack.imgur.com/Qggv2.png)


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2022-03-15 15:57:33

------------

Wrap your table with a div with overflow-x class and your required width, and add w-full class to your table class.
```
<div class='overflow-x'>
        <table class='table-auto overflow-scroll w-full'>
            <thead>
                <tr class='bg-gray-100'>
                    <th class='w-20 px-4 py-2'>No.</th>
                    <th class='px-4 py-2'>First Name</th>
                    <th class='px-4 py-2'>Second Name</th>
                    <th class='px-4 py-2'>Third Name</th>
                    <th class='px-4 py-2'>Department</th>
                    <th class='px-4 py-2'>Stage</th>
                    <th class='px-4 py-2'>Email</th>
                    <th class='px-4 py-2'>Roles</th>
                    <th class='px-4 py-2'>status</th>
                    <th class='px-4 py-2'>University Email</th>
                    <th class='px-4 py-2'>University Password</th>
                    <th class='px-4 py-2'>Students Files</th>
                    <th class='px-4 py-2'>Actions</th>
                </tr>
            </thead>
            <tbody>
                @if(isset($users))
                @include('dashboard.users.partials.users_details') @endif
                @if(isset($searches))
                @include('dashboard.users.partials.search') @endif
                @if(isset($statusSearch))
                @include('dashboard.users.partials.status_search') @endif
            </tbody>
        </table>
    </div>
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-15 15:56:21

------------

```
<div class="overflow-auto ..."></div> you can try this
```

[tailewindcss link](https://tailwindcss.com/docs/overflow#scrolling-if-needed)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-10 03:31:49

------------

You can add these classes to your table to make your table scrollable on the x axis
```
<table class="table-auto overflow-x-scroll w-full block">
                    <thead>
                    <tr class="bg-gray-100">
                        <th class="w-20 px-4 py-2">No.</th>
                        <th class="px-4 py-2">First Name</th>
                        <th class="px-4 py-2">Second Name</th>
                        <th class="px-4 py-2">Third Name</th>
                        <th class="px-4 py-2">Department</th>
                        <th class="px-4 py-2">Stage</th>
                        <th class="px-4 py-2">Email</th>
                        <th class="px-4 py-2">Roles</th>
                        <th class="px-4 py-2">status</th>
                        <th class="px-4 py-2">University Email</th>
                        <th class="px-4 py-2">University Password</th>
                        <th class="px-4 py-2">Students Files</th>
                        <th class="px-4 py-2">Actions</th>
                    </tr>
                    </thead>
                    <tbody>

                        @if(isset($users)) @include('dashboard.users.partials.users_details') @endif
                        @if(isset($searches)) @include('dashboard.users.partials.search') @endif
                        @if(isset($statusSearch)) @include('dashboard.users.partials.status_search') @endif

                    </tbody>
                </table>
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-03-15 15:51:14

------------

You can put it all into a div with a fixed width and height, then use
overflow: scroll;
like this
```
<style>
 #table {
    width: 50%;
    height: 100%;
    overflow: scroll;
}
</style>
 <div id="table">
 <table>
                    <thead>
                    <tr class="bg-gray-100">
                        <th class="w-20 px-4 py-2">No.</th>
                        <th class="px-4 py-2">First Name</th>
                        <th class="px-4 py-2">Second Name</th>
                        <th class="px-4 py-2">Third Name</th>
                        <th class="px-4 py-2">Department</th>
                        <th class="px-4 py-2">Stage</th>
                        <th class="px-4 py-2">Email</th>
                        <th class="px-4 py-2">Roles</th>
                        <th class="px-4 py-2">status</th>
                        <th class="px-4 py-2">University Email</th>
                        <th class="px-4 py-2">University Password</th>
                        <th class="px-4 py-2">Students Files</th>
                        <th class="px-4 py-2">Actions</th>
                    </tr>
                    </thead>
                    <tbody>

                        @if(isset($users)) @include('dashboard.users.partials.users_details') @endif
                        @if(isset($searches)) @include('dashboard.users.partials.search') @endif
                        @if(isset($statusSearch)) @include('dashboard.users.partials.status_search') @endif

                    </tbody>
                </table>
</div>
```

of course set the width and height to whatever you need.


------------
    
    