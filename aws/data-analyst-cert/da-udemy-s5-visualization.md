### Course Material
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

## Amazon Quicksight
---  
### *Overview*
- fast, easy, cloud-powered business analytics service
- allows all employees in an organization to:
    - build visualizations
    - perform ad-hoc analysis
    - quickly get business insights from data
    - anytime, on any device ( browsers, mobile )
- serverless  

**Data Sources**  
Data preparation allows limited ETL  
`Redshift`, `Aurora`, `Athena`, `EC2-hosted databaes`  

Files (`S3` or on-premises ):
- excel
- csv, tsv
- common or extended log format

> If you want to actually have a way of visualizing data in an excel spreadsheet, `Quicksight` can do that.


**SPICE**
> the idea of `SPICE` is to accelerate your queries
- data sets are imported into `SPICE`
    - Super-fast, Parallel, In-memory Calculation Engine
    - uses columnar storage, in-memory, machine code generation
    - accelerates interactive queries on large datasets
- each user gets 10GB of `SPICE`
- highly available / durable
scales to hundreds of thousands of users
- can accelerate large queries that would time out in direct query mode ( hitting `Athena` directly )
    - but if it takes more than 30 minutes to import your data into `SPICE` it will still time out

**Anti-Patterns**
- highly formatted canned reports
    - `Quicksight` is for ad-hoc queries, analysis and visualizations
- ETL
    - use `Glue` instead, although `Quicksight` can do some transformations

**Security**  
- multi-factor authentication on your account
- `VPC` connectivity
    - add `Quicksight's` IP address range to your database security groups
- Row-level security ( Enterprise Edition Only )
- Private `VPC` access
    - Elastic Network Interface, `AWS Direct Connect`
- resource access
    - must ensure `Quicksight` is authorized to use `Athena` / `S3 / your buckets
    - this can be managed within the `Quicksight` console 
- data access
    - can create `IAM policies` to restrict what data in `S3` is given to `Quicksight` users

**Quicksight & Redshift: Security**
- by default `Quicksight` can only access data stored IN THE SAME REGION as the one `Quicksight` is running within
- a `VPC` configured to work across AWS regions will not work

`Solution`:
- create a new security group with an inbound rule authorizing access from the IP range of `Quicksight` servers in that region

**User Management**
- `Users` defined via `IAM`, or email signup
- Active Directory connector with `Quicksight Enterprise Edition`
    - all keys are managed by `AWS`; you CANNOT use customer provided keys
    - Enterprise edition only
    - can tweak security access using `IAM` if needed

### *Pricing*
- Annual Subscription
    - Standard: $9 / user / month
    - Enterprise $18 / user / month
- Extra SPICE capacity (beyond 10GB )
    - $0.25 ( standard ) $0.38 ( enterprise ) / GB / user / month
- Month to month
    - Standard: $12 / user / month
    - Enterprise: $24 / user / month
- Enterprise edition:
    - Encryption at rest
    - Microsoft Active Directory integration

### *Dashboards*
- read-only snapshot of an analysis
- can share with others with `Quicksight` access
- can share even more widely with *embedded dashboards*
    - embed within an application
    - authenticate with Active Directory / Cognito / SSO
    - `Quicksight` Javascrip SDK / Quicksight API
    - Whitelist domains where embedding is allows

### *ML Insights*
- ML-powered anomaly detection
    - uses `Random Cut Forest`
    - identify top contributors to significant changes in metrics
- `ML-powered forecasting`
    - also uses `Rancom Cut Forest`
    - Detects seasonality and trends
    - Excludes outliers and imputes missing values
- Autonarratives
    - Adds "story of your data" to your dashboards
- Suggested Insights
    - "Insights" tab displays read-to-use suggested insights

### *Quicksight Q*
- machine-learning-powered
- answers business questions with `Natural Language Processing`
    - "What are the top-selling items in Florida?"
- offered as an add-on for given regions
- personal training on how to ue it is required
- must set up `topics` associated with `datasets`
    - `datasets` and their fileds must be NLP-friendly
    - how to handle dates must be defined

### *Choosing Visualization Types*
**Quicksight Visual Types**
- `Autograph` - tries to select the most appropriate visualization type for the data you have selected
- `Bar Charts` - for comparison and distribution ( histograms )
- `Line Graph` - for changes / trends over time
- `Scatter Plots`, `Heat Maps` - for correlation
- `Pie Graphs`, `Tree Maps` - for aggregation
- `Pivot Tables` - for tabular data
- `KPIs` - Key Performance Indicators
- `Geospatial Charts`
- `Donut Charts` - percentage of a given thing for the total amount
- `Gauge Charts`
- `Word Clouds`

## *Other Visualization Tools*
---  
- Web-basd visualization tools ( deployed to the public )
    - D3.js
    - Chart.js
    - Highchart.js
- Business Intelligence Tools
    - Tableau
    - Microstrategy

> A little bit different from `Quicksight` in that this is really intended to be deployed to the public.
> It is not intended to be deployed to users of your organization or people within your organization.
> This is meant for a Website that is deployed to the Web.