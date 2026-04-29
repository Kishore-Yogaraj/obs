I think we finally found ourselves back in a groove so lets work off this momentum. I'm going to work at this cafe until 4:30 so that gives me around 3 hours to get a good chunk of work term. I'm going to be a serial entrepreneur.

We have two things we need to work on today which is Savor and Preparing for the job interview. The ultimate goal with Savor is to get it launched on the app store as soon as possible. 

Some things I know that needs to be done for Savor off the top of my head:
- Need to test how it looks on the home screen after its downloaded
- Need to make sure there's a proper splash page
- Need to make sure all our details are loaded onto the supabase dashboard and are being reflected on the app
- Need to use code rabbit to fix any issues there might be in the code

For the interview we're just going to do our usual prep and work from there

First lets get all our deals loaded into the app


```SQL

BEGIN
  RETURN QUERY
  SELECT 
    r.id,
    r.name,
    r.category,
    r.opening_time,
    r.closing_time,
    COALESCE(COUNT(d.id), 0) as deal_count
  FROM restaurants r
  LEFT JOIN deals d ON r.id = d.restaurant_id
  GROUP BY r.id, r.name, r.category, r.opening_time, r.closing_time
  ORDER BY deal_count DESC;
END;

```

We're about to completely commit to building out this app. Before you do this I want you to remember why you're deciding to do this. 