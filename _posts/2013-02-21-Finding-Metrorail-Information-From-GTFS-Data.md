---
layout: post
title: Filtering Metrorail Information from WMATA GTFS Data
tags: WMATA
---

General Transit Feed Specification, or GTFS, is the format WMATA publishes schedule data information; it also powers their trip planner. If you were only interested in MetroRail data, you're going to have to filter out the packaged bus transit data, which reduces file sizes considerably. Unix commands grep and awk are very helpful in getting the job done.

In order to determine when the next train will arrive at a given station, three files are included in the GTFS information: routes.txt, trips.txt, and stop_times.txt. In CSV format, the routes.txt details each possible route in the WMATA system:

{% highlight sh lineos %}
routes.txt

route_id,agency_id,route_short_name,route_long_name,route_type,route_url,route_color
...
BLUE,2,"Blue","Metrorail Blue Line",1,"",0d7bba
260,2,"","Richmond Highway Express Bus",3,"",
GREEN,2,"Green","Metrorail Green Line",1,"",009d57
...
{% endhighlight %}

Removing bus information is relatively straightforward.

{% highlight sh %}
grep Metrorail routes.txt >> routes_trimmed.txt
{% endhighlight %}

Piping the trimmed data to a file is helpful for using it as input for future commands. Next, I trimmed trips.txt to remove bus trips.

{% highlight sh lineos=table %}
trips.txt

route_id,service_id,trip_id,trip_headsign,direction_id,block_id,shape_id
...
119,1,12722,"GREENBELT STATION",1,,739
119,1,12723,"GREENBELT STATION",1,,739
119,1,12724,"GREENBELT STATION",1,,739
...
BLUE,2,32891,"FRANCONIA-SPRINGFIELD",0,,1726
BLUE,2,32892,"FRANCONIA-SPRINGFIELD",0,,1726
BLUE,2,32893,"FRANCONIA-SPRINGFIELD",0,,1726
...
{% endhighlight %}

Bus information is mixed with Metrorail. Upon inspection, I noted the data I was interested in contained the color of the line. But I couldn't simply grep the line color because there are columns in the trip_headsign (e.g., GREENBELT) that conveniently include line color. Being crafty, I assumed that all trips that I wanted had the trip_id followed by a comma.

{% highlight sh %}
grep 'BLUE,\|RED,\|YELLOW,\|GREEN,\|ORANGE,' trips.txt >> trips_trimmed.txt
{% endhighlight %}

With the following grep command, I was able to filter what I wanted. However, the best option (to promote code resuability) would be to match the route_id at first column in routes.txt with the route_id in the first column in trips.txt. The code would continue to work, for example, if WMATA changed the route identifer for Metrorail service. Now I had a list of each trip's starting point.

In order to find the time that each of these trips left, I needed to use awk, which is more powerful than grep. Here is sample stop_times.txt data:

{% highlight sh %}
stop_times.txt

trip_id,arrival_time,departure_time,stop_id,stop_sequence,pickup_type,drop_off_type,shape_dist_traveled
1,05:19:06,05:19:06,11558,1,0,0,0.1794
...
32891,06:54:00,06:54:00,4697,1,0,0,0.0000
32891,06:57:00,06:57:00,4664,2,0,0,1.3634
{% endhighlight %}

The only stop times applicable to metrorail service are those that have a trip_id present in the trimmed trips.txt. To find those times, I used awk. The syntax of awk may be hard to follow with no prior experience, so I'll walkthrough the command.  

{% highlight sh %}
awk -F, 'FNR==NR{f1[$3];next}$1 in f1' trips_trimmed.txt stop_times.txt > stop_times_trimmed.txt
{% endhighlight %}

"-F," specifies the comma as the field delimiter. The 'program' to execute is specified inbetween single quotes. "FNR==NR" means 'iterate over each record until the end-of-file is reached, and execute the following command. A record is a single line from the supplied input. f1[$3] both initializes an associative array (conceptually, a hash map) named f1, with the key being the element in the third column ("$3") of the first input file (trip_id in trips_trim.txt). Note that the starting index is 1 and not 0. "next;" is to move to the next record. The code following the curly braces applies to the second input file: stop_times.txt. "FNR=NR" still applies: for each record in stop_times.txt, if the element at the first column ("$1") is a key in the array f1, print the line.

From the trimmed stop_times.txt, and extrapolating critical information from calendar_dates.txt, I was able to generate cool statistics such as: 10,492 trains are run per week on regular service, making 237,096 stops at 86 stations.