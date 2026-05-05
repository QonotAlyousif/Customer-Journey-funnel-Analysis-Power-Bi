# E-commerce Customer Journey & Funnel Analysis (Power Bi)

 The project analyze e-commerce customer behavior across platform using synthetic datasetm The dashboard provide insights into how the user
 move from **Home** > **Product Page** > **Cart** > **Checkout** > **Confirmation**, highlighting Conversions, Drop-offs, and behavioral patterns 
 
## Objectives 
- Analyze customer journey across funnel stages.
- Measure conversion rate and cart abandonment rate
- Compare customer behaviour across Device type, Countries, Sources. 
- Understand the impact of traffic hours and time spent on pages on Conversion.

## Dataset 
A synthetic dataset was generated with AI tool (ChatGPT) to simulate real-world behavoir containing 5000 seasons over one year. 
### Dataset features: 
 - Non-uniform distributions ( realistic traffic patterns)
 - Seasonal variations (Monthly and Daily)
 - Logical Behavoir

## Data Model view 
<img width="1765" height="1071" alt="image" src="https://github.com/user-attachments/assets/ae294fd7-b4aa-4ecb-83d0-bbade52fe121" />

## Dashboard Structure 
 **Page 1**: Funnel Analysis
<img width="1912" height="1222" alt="image" src="https://github.com/user-attachments/assets/a7d4e335-2ccb-4a21-970b-60528bab471f" />


 **Page 2**: User Behavior Analysis
<img width="1911" height="1222" alt="image" src="https://github.com/user-attachments/assets/4c289ecd-ce39-41cc-90a9-f83133537bb6" />

## KPIs DAX 

### Conversion rate 

1- Calculate the number of sesssions
```
Sessions = DISTINCTCOUNT('customer_journey'[SessionID])
```
2- Calculate Purchased sessions
```
Purchased Sessions = 
CALCULATE(
    DISTINCTCOUNT('customer_journey'[SessionID]),
    'customer_journey'[Purchased] = 1
)
```
3- Find the conversion rate 
```
Conversion Rate = 
DIVIDE([Purchased Sessions], [Sessions])
```

### Cart abandonment rate 
1- Caluclate the abandoned cart sessions 
```
Abandoned Cart Sessions = 
CALCULATE(
    DISTINCTCOUNT('customer_journey'[SessionID]),
    'customer_journey'[PageType] = "cart",
    'customer_journey'[Purchased] = 0
)
```
2- Calculate the cart abandomnet rate 
```
Cart Abandonment Rate = 
DIVIDE(
    [Abandoned Cart Sessions],
    [Abandoned Cart Sessions] + [Purchased Sessions]
```
### Drop-off rate 

Calculate Drop-off rate across pages
```
Drop-off Rate = 
VAR CurrentPage = SELECTEDVALUE('customer_journey'[PageType])

VAR CurrentSessions =
    CALCULATE(
        DISTINCTCOUNT('customer_journey'[SessionID]),
        'customer_journey'[PageType] = CurrentPage
    )
VAR NextPage =
    SWITCH(
        CurrentPage,
        "home", "product_page",
        "product_page", "cart",
        "cart", "checkout",
        "checkout", "confirmation"
    )

VAR NextSessions =
    CALCULATE(
        DISTINCTCOUNT('customer_journey'[SessionID]),
        'customer_journey'[PageType] = NextPage
    )

RETURN
IF(
    ISBLANK(NextPage),
    BLANK(),
    "🔻 " & FORMAT(
        1 - DIVIDE(NextSessions, CurrentSessions),
        "0.0%"
    )
)
```

### Traffic hours

1- Create hours range 
```
Hour Range = 
VAR StartHour = FLOOR('customer_journey'[Hour], 6)
VAR EndHour = MOD(StartHour + 6, 24)

RETURN
FORMAT(TIME(StartHour,0,0),"h AM/PM")
& " - " &
FORMAT(TIME(EndHour,0,0),"h AM/PM")
```

2- Find Peak traffic hour
```
Peak Hour = 
VAR _table =
    SUMMARIZE(
        'customer_journey',
        'customer_journey'[Hour],
        "Sessions", DISTINCTCOUNT('customer_journey'[SessionID])
    )

VAR _top =
    TOPN(1, _table, [Sessions], DESC)

VAR _hour =
    MAXX(_top, 'customer_journey'[Hour])
RETURN
FORMAT(
    TIME(_hour, 0, 0),
    "h:mm AM/PM"
)
```
## Key isssues Identified 
- High drop-off at product page
- High cart abandonment (34%)
- Checkout friction
- Weak mobile performance
- Limited traffic diversification

## Recomenndations 

- **Improve Checkout Experience**: Simplify checkout steps, Enable guest checkout ,Offer multiple payment options.
- **Enhance Mobile Experience**: Optimize page speed, Simplify navigation and forms. 
- **Retarget Abandoning Users** Use email reminders for abandoned carts
- **Diversify Marketing Channels** Invest in social media campaigns. 
- **Time-Based Strategies** Schedule campaigns during peak hours (morning).
- **End-of-Month Strategy** Launch promotions before the end of the month. 


