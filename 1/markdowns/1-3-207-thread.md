
# Post \#64193452 [Link](https://stackoverflow.com/questions/64193452/)

## Evaluating the end time of a period of time

**Vote**: 4 (532/702) **Views**: 7102 (480/702) 

**Internal ID** \#1-3-207

Created at 2020-10-04 10:22:06

Tags: `python-3.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am working on a simple code able to evaluate the end time of a period of time, given as a number of minutes (it could be arbitrarily large). The start time is given as a pair of hours (0..23) and minutes (0..59). The result has to be printed to the console.

So far I tried below code and finding difficult to get hours calculation please help.
```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))
mins = (mins + dura)%60
hour = **** Please help this calculation ****
print(hour, ":", mins, sep='')
#expected output is 13:16 (ex: given hour = 12 , mins = 17 , dura = 59 )
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2020-10-04 11:00:45

------------

Try this:
```
from datetime import date, datetime, time, timedelta


if __name__ == '__main__':
    hour = int(input("Starting time (hours): "))
    mins = int(input("Starting time (minutes): "))
    dura = int(input("Event duration (minutes): "))
    dt = datetime.combine(date.today(), time(hour, mins)) + timedelta(minutes=dura)
    print(dt.time().strftime("%H:%M"))
```

Output: `13:16`


------------
    
    
## Answer \#1

 Vote: 4

Created at 2020-11-05 03:35:52

------------

```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))
time_hour = (hour + dura//60 + (mins+ dura%60)//60)%24
time_min = (mins+ dura%60)%60
print("It will end at " + str(time_hour) + ":" + str(time_min))
```

It will end at `13:16`


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-03-05 04:03:58

------------

I understand this has been answered. However I ran across this exact problem in some practice work for learning python. The end result just wanted the time in HH:mm format.
```
#given hour = 12 , mins = 17 , dura = 59

hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))
hour = (hour + dura//60 + (mins+ dura%60)//60)%24
mins = (mins+ dura%60)%60
print(hour,":",mins,sep="")

13:16
```

The last line was requested like this. I understand this was largely explained and answered, but I saw a difference in syntax and wanted to share my results.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-04-06 02:32:43

------------

Try This:
```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))
mins = mins + dura # find a total of all minutes
hour = hour + mins // 60 # find a number of hours hidden in minutes and update the hour
mins = mins % 60 # correct minutes to fall in the (0..59) range
hour = hour % 24 # correct hours to fall in the (0..23) range
print(hour, ":", mins, sep='')
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-09-22 09:12:18

------------

```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))

print ("Event ending time: ",(hour + (mins + dura)//60)%24, ":", (mins + dura%60)%60, sep="")# Write your code here.
```

At the level the question is asked, it requires us to find the answer with one line of code. So, This is what i came up with.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-10-04 11:06:10

------------

Try this is if you want pure math
```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))

hour = hour +int((mins+dura)/60)
mins = (mins + dura)%60
while(hour/24>=1):
    hour=hour-24
  
print(hour, ":", mins, sep='')
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-10-02 11:22:20

------------

```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))
hr=((((hour*60+mins)+dura)//60)%24)
min=((hour*60+mins)+dura)%60 
print(hr,min,sep=":")
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2021-12-26 20:54:25

------------

```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))


hours = (hour + (mins+dura)//60)%24
minutes = (mins+dura)%60
print(hours, ":",minutes)
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2022-01-11 20:51:49

------------

I just found this exercise in a course and managed to solve it this way:
I know the code it's too large, was my first try solving a problem in Python, but I know it can be written shorter.
```
# Data input
start_hour = int(input('Enter start hour: '))
start_minute = int(input('Enter start minute: '))
event_duration = int(input('Enter event duration (min): '))

# Adding the minutes
total_minutes = (start_minute + event_duration)

# Calculating the hours
calc_hours = total_minutes / 60

# Calculating the residual for minutes
min_residual = int(total_minutes % 60)

# Calculating the residual for hours
hrs_residual = int((calc_hours + start_hour) % 24)

# Printing the answer
print('\nThe event end at:')
print(hrs_residual,':',min_residual)
```



------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-06-07 23:17:38

------------

```
hour = int(input("Starting time (hours): "))
mins = int(input("Starting time (minutes): "))
dura = int(input("Event duration (minutes): "))
```

#here we will get the minutes part by adding all the minutes and get the excess minutes.. the excess minutes will be the minutes part..
```
minutePart = (mins + dura)%60
```

#here we will get the hours part by 1- adding all the minutes and get the int part of the result adding to the given minutes in the inputs then get the excess hours after dividing by 24 ..
```
hourPart = (((mins + dura)//60)+hour)%24
print("event will end at " , hourPart , ":" ,minutePart , sep="" )
```

I've tested the code with all the possibilities and it is working as expected.


------------
    
    