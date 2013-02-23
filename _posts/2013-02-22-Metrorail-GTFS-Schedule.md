---
layout: post
title: Metrorail GTFS Schedule Analysis
tags: WMATA
---

As mentioned in Thursday's post, I was able to calculate the number of trains Metro runs on for a full week of regular service, and the number of stops made over one week. calendar_dates.txt is the GTFS file WMATA uses to specify the schedule. That is, given a calendar day <code>date</code>, for the specified <code>service_id</code>, the service is scheduled operates iff the <code>exception_type == 1</code>. For WMATA's GTFS data, all service is defined as exception type 1 (whereas 0 removes it from the schedule), so this file is necessary to know if a scheduled stop times occur on a given day. Here's sample data from calendar_dates.txt:

{% highlight sh %}
calendar_dates.txt

service_id,date,exception_type
1,20130218,1
2,20130126,1
2,20130202,1
2,20130209,1
...
{% endhighlight %}

To determine the meaning of the service_id's, I ran the following grep command for a few weeks of information.

{% highlight sh %}
grep yyyymmdd calendar_dates.txt
<output>
{% endhighlight %}

Here is that information in an easier-to-understand format:

<img src="../../../../images/wmata_service_id.png"/><br/>

2/18/2013 seemed to have some exceptions, but the last two weeks generated a "typical" rail schedule, which I used to for the analytics. For example, I assumed any record in stop_times with a service_id of 8 occured 5 times in a single week, based on the chart. To find the number of stops made by trains in a week, I wrote the following awk script:


{% highlight sh %}
awk -F, '
FNR==NR
{f1[$3]=$2}
{if (f1[$1] == 2)      { train += 1; next;
} else if(f1[$1] == 3) { train += 1; next;
} else if(f1[$1] == 4) { train += 5; next;
} else if(f1[$1] == 5) { train += 1; next;
} else if(f1[$1] == 6) { train += 4; next;
} else if(f1[$1] == 7) { train += 4; next;
} else if(f1[$1] == 8) { train += 5; next;
} else { next; }}
END{ print train }
' trips_trim.txt stop_times_trim.txt
{% endhighlight %}

By mapping the trip_id to the service_id, I could look up the service_id in the stop_times file and increment a count based of the number of times that service_id occured in one week. That led to a count of <strong>214,358</strong> stops made by trains weekly.