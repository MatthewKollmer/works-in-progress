# About

These contents are the preliminary code and data used to identify racial violence victims in historical newspapers. Below is a process journal as well. I update this journal regularly. Initially, it was just to help me keep track of tasks and the overall process, but it's turned into a broader resource. It has helped collaborators understand what I'm doing, and it showcases _process_ in DH/cultural analytics work more broadly. It is not polished academic writing or anything. But feel free to read along to see how the US Lynching Victims Project (a subproject of the larger [Virality of Racial Terror Project](https://www.racialviolencearchive.com/vrt-project.html)) is going.

Also, full disclaimer: I often collaborate with ChatGPT to make corrections to code if I get errors and I ask it questions about Python libraries I'm not familiar with.

Also, larger files are not hosted here. They can be found in this Box folder: 

[https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud](https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud)

If you don't have access via this link, please [contact me](https://matthewkollmer.com/contact/). It just means I need to approve your email to get Box access.

### Sept 3 Update

Here's a step-by-step breakdown of what I've done so far:

1) Using Seguin & Rigby's lynching dataset ([see here](https://journals.sagepub.com/doi/pdf/10.1177/2378023119841780) and [here](https://archive.ciser.cornell.edu/studies/2833/data-and-documentation)), I extracted names of Black victims between 1883 and 1921, and cities where their murders occurred. If full names or cities were missing, I didn't include them in my subset of the data. This resulted in about 450 instances with victims and places named. See my [preprocessing notebook](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/preprocessing.ipynb) for this code.
2) I put the names, cities, and years of occurrence into a pipeline that scrapes Chronicling America search results. This scraping pipeline identifies any pages from the year of occurrence where the victim name and city appears within 100 words of each other. Then it scrapes the newspaper content of all those pages. The scraped contents were saved to separate csv files (separated by victim names). This resulted in about 350 victim clusters and a total of about 19,000 digitized pages. See my [scraping notebook](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/build_newspaper_dataset.ipynb) for this code.
3) Then I did a little bit of post-processing (more to be done yet). I corrected minor OCR errors (added spaces where they were missing between victim names) and created clippings of the newspapers around instances of victim names (the 100 words before and after victim names). See my [post-processing notebook](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/postprocessing.ipynb) for this code.
4) Look to the lynch_clusters directory in my [Box folder](https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud) to find the results–about 350 .csv files titled after each victim's name, with pages where they–and the city of their murder–was named.

### Sept 10 Update

Over this week, I enriched the lynch_clusters with geolocation data derived from the Viral Texts' [place metadata](https://github.com/ViralTexts/newspaper-metadata) for newspapers. I also used some Python libraries to build maps of the reprints. The code for these activities can be viewed here: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/geolocate_reprints.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/geolocate_reprints.ipynb).

I also explored the data using these interactive maps. This led to an important discovery: there are a large number of false positives in the data (i.e., instances where the victim's name appears, but not in reference to racial violence). I need to review my scraping code and ChronAm's search. I think I'll need to rebuild the dataset with more refinement. I'm thinking of scraping from a more general search with just the victim's name appearing on the page. Then I'll mine the results for instances where city name and other markers of racial violence occur (words like 'lynch', 'posse', 'mob', 'negro', etc.) within a certain range of the victim names.

Anyway that's not a total loss. In rebuilding the dataset, I'll easily be able to expand by some variables we've discussed in meetings, such as the year following the year of incident and victim aliases. Once I've tried to build the dataset with more refinement, I'll rerun these geolocation and mapping scripts, too, and see how things are looking.

### Sept 18 Update

This week I was admittedly more cluttered in my workflow, but here's what I got done:
- I built a larger dataset based solely on victim names (as opposed to victim names plus city names appearing together). I did this by re-scraping Chronicling America by just victim name in the year of the incident and the year following.
- I debugged some things. I noticed my fix_names() and newspaper_clippings() functions needed to be improved. Basically, I added spaces before and after any fixed_names() then used nltk to tokenize the data to make newspaper_clippings() more accurate in its clippings.
- I added new columns to this broader dataset (victim_name, city, state).
- I added a city_mentioned column with binary logic. If city name is mentioned in the clipping, it is labelled 'yes'.
- I added a signal_word_count column. It contains counts of predetermined signal words (words that probably indicate racial violence) as they appear in the clippings.
- Using these last two columns, I've created a subset of the data where city name appears near victim name and/or racial violence words appear near the victim name. This subset contains just over 10,000 hits. It needs to be reviewed, but I anticipate it will contain fewer false positives of racial violence than the data from my first iteration. I'll also need to test my thresholds to see where I can identify the most instances with fewest false positives. What combination of variables–city of occurrence, what signal words–will most accurately indicate racial violence reports? Not sure how I'll deduce that yet, but I'm thinking on it.

All these steps can be viewed in this messy Jupyter notebook: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/build_refine_dataset.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/build_refine_dataset.ipynb)

### Sept 19 Update

In the last day, I've tried to bring it all together. I've added more columns to the new, refined dataset (columns for 'newspaper' and 'reprint_date'). I also remade the maps with the refined dataset. I also went ahead and created a web page to view the map demo. It is password protected. Contact me if you need the password. But the page can be viewed here: [https://matthewkollmer.com/vrt-us-lynching-victims-demo/](https://matthewkollmer.com/vrt-us-lynching-victims-demo/).

Any changes in my process are reflected in the Jupyter notebooks in this repository now, too. I also uploaded the refined data and the maps to the Box folder here: [https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud](https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud)

I think the next thing I need to focus on is testing the accuracy of my racial violence detection. Here's a pseudocode version of how I've narrowed the detection:

Each instance labelled a racial violence report:

- contains the victim's name
- was printed in the year of the occurrence or the year after
- either contains the city of the occurrence within 150 words of the victim's name
  OR
- three or more signal words (lynch, mob, posse, negro, shot, hang) within 150 words of the victim's name

So, how accurate are these results? I'm not quite sure yet. I'm also not sure how to test their accuracy other than combing through them. I'm certain they're more accurate than my first iteration (described in Sept 3 and Sept 10 updates above), but perhaps I can add more signal words or essentialize the city of occurrence to improve accuracy? Again, not sure. I'll continue to think on this over the next few days.

### Sept 27 Update

This week was a week of cogitation. Less coding, more reviewing of the data and pondering what to do with it. At the suggestion of Dr. Ward, I took a look at these resources: [https://journals.sagepub.com/toc/anna/694/1](https://journals.sagepub.com/toc/anna/694/1). I've also been reading a copy of James Loewen's _Sundown Towns_. I've been thinking about how to go further than merely mapping this data. What else can I do with it? I think finding ways to showcase it is valuable, but I also want to interrogate it programmatically and derive information from it. One thing I'm considering is creating embeddings of victim names. How are they treated similarly or differently from one another? Perhaps embeddings would help me answer this question. It may also help me find outliers–victims described in semantically different ways. Perhaps I could create a comparative dataset and embeddings with white victims, too.

I'm also thinking about building datasets of newspapers from regions where lynchings occurred and study what they were publishing ahead of these cases. I think this would be entirely exploratory to start, but the goal would be to find patterns of printed texts–maybe other crimes, other racially charged instances, vigilant committees, etc.–that intimate that the populace was more racially charged and likely to commit acts of racial violence. To be clear, I have no idea how exactly I would mine the data to make these kinds of inferences or predictions, but I could try to figure it out.

Anyway, alongside Avery, I also reviewed the data to see how accurately the clippings are capturing instances of racial violence. I did this in a pseudo-programmatic way. Basically, I built a loop to prompt me to review each row and label the clipping as either totally about racial violence, partially about racial violence, not about racial violence at all, or unknown. This process can be reviewed here: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/case_match_review.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/case_match_review.ipynb). I looked at 171 rows and four csv files. A little over 90% of them were totally or partially about their respective racial violence events. So, that's encouraging–it means that the refined dataset I've put together probably isn't cluttered with false positives. But we'll see how it went for Avery, too. And of course, we should probably review far more of the data. There are something like 10,000 rows. A couple hundred of them is still a small sample size.

Oh, almost forgot–a large part of my week was also preoccupied with reviewing Chronicling America's interface and API changes, which are set to be implemented at the end of this year... Ugh, this is a bit of a nightmare. Once they change the API, most of my code will be broken. I'm going to need to change my scraping approaches drastically. I'm looking more closely at downloading their batches of files rather than scraping by URL. It's going to be a different process altogether, but nothing to be done about it. Must plan accordingly.

### Oct 1 Update

Updates: I decided to forge ahead on the local/regional newspapers idea–that is, the plan to identify towns where lynchings occurred and where nearby local or regional newspapers are 1)digitized and 2) show coverage around the same timeframe. Toward those ends, I put together this notebook: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/identify_build_lynching_town_datasets.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/identify_build_lynching_town_datasets.ipynb). Basically, this notebook contains four general steps:

1) finding localities (city, state) in our data where lynchings occurred
2) Geolocating these lynching towns
3) identify lynching towns with digitized newspapers
4) building lynching town subsets

These steps were successful, but I ran into some surprises along the way. Firstly, after reviewing our Black subset of Seguin & Rigby's data, I discovered it contains very few lynchings from the majority of the Southern states?! This surprised me greatly. There are lots of instances from Texas, but not Louisiana, Mississippi, Georgia, Alabama, or Florida. I need to investigate why. I touch on one hypothesis in the notebook (is it because I removed instances without named victims?), but I haven't investigated this anomaly further.

Secondly, I was surprised by how difficult it was to find towns where lynchings occurred and where there was relevant available newspaper data. Now, this part of the process is ongoing, but I had to review by hand the Chron Am map of newspapers ([see here](https://loc.maps.arcgis.com/apps/instant/media/index.html?appid=3c6a392554d545bdb1c083348ef56458&center=-97.5126;39.6376&level=3)) and my own map of lynchings. This process really revealed the limitations of the Chron Am data. As I say in my notebook, "the limitations of Chron Am's data reveals itself here in ways you don't realize looking at the data as rows or lists. [...] the map makes you see the gaps in spaces where surely there were newspapers, but they are not digitized or available." 

Still, I was able to find a few overlapping spaces and cases. I was also able build subsets of these newspapers by scraping Chron Am. Those subsets can be downloaded here: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/lynching_town_newspapers.zip](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/lynching_town_newspapers.zip). They are:

Newspaper: Peninsula Enterprise, Acconomac, VA 
Page: https://chroniclingamerica.loc.gov/lccn/sn94060041/ 
Incident: Magruder Fletcher, Tasley, VA

Newspaper: Maryland Independent, La Plata, MD 
Page: https://chroniclingamerica.loc.gov/lccn/sn85025407/ 
Incident: Joseph Cocking, Port Tobacco, MD

Newspaper: Lexington Intelligencer, Lexington, MO 
Page: https://chroniclingamerica.loc.gov/lccn/sn86063623/ 
Incident: Harry Gates, Lexington, MO

Now, what to do with these newspaper subsets... I'll begin by reading them. And seeing if they mention anything related to their respective cases. That's what's on the docket for the next few days.

### Oct 15 Update

I'm overdue for an entry here. In the past two weeks, I've been busy on a few fronts with the project. It's growing tendrils, this thing. First, there's the lynching town paper analysis and the pipeline for identifying more lynching town papers. I've written some preliminary, exploratory code for studying lynching town papers: [lynch_town_paper_analysis.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/lynch_town_paper_analysis.ipynb). This notebook contains written considerations and visualizations, but to summarize briefly: I've tried embeddings, word frequency stuff, and topic modeling, but I need to anchor my analyses more thoughtfully. And as for a pipeline for identifying more lynching town papers, I'm on it, but I haven't finished a notebook worth uploading quite yet.

Part of my delay in that regard is the tendril with the largest potential: the additions of the Tolnay-Beck Inventory. You may remember from  [identify_build_lynching_town_datasets.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/identify_build_lynching_town_datasets.ipynb) that I noticed a glaring geospatial gap in our data–there were no recorded lynchings from the Deep South. Well, it turns out that was because Seguin/Rigby's data implicitly tried to expand lynching data to a national scale. They did this because the Tolnay-Beck Inventory–an earlier and widely regarded dataset of lynchings–had recorded only lynchings from the Deep South. In turn, I had to find the Tolnay-Beck Inventory. The PIs for VRT were gracious enough to send me a copy they had from 2016. I also reached out to Dr. Amy Bailey at UIC who maintains the dataset today. She sent me the latest version (last updated 2022).

Anyway, this is a tendril with a lot of potential because the Tolnay-Beck Inventory contains about 3,500 more victims relevant to this project. That's ten times as many as the Seguin/Rigby data... I've done some first steps in preparing this data for the scraping process (see [tolnay_beck_additions.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/tolnay_beck_additions.ipynb)). However, it's gonna take some more planning before I scrape all those results. For reference, scraping Chron Am for the Seguin/Rigby data took about nine hours of runtime. If I have to scrape ten times that amount, it'll take about 90 hours. If possible, I'd like to find a workaround. Otherwise, I'll have to scrape the results in batches over the course of a couple weeks.

We'll see. Tomorrow's meeting will hopefully give me more insight. But for the rest of this week, I'll prepare for a presentation on this project in the Data Cultures Lab (an informal DH lab at UIUC). I'll also see if I can finish the lynching town paper identification pipeline. Lots to do! Tendrils, I say. There's also reviewing the results more thoroughly, improving the signal word lists/lexicon, and other stuff. I think this Friday's presentation will be a good opportunity to get all these things in order.

### Oct 17 Update

Tomorrow I have the presentation for the Data Cultures Lab. As part of my preparation, I put together this flowchart:

![Flowchart for DCL Presentation](https://github.com/MatthewKollmer/works-in-progress/raw/main/vrt_work/us-lynching-victims/dcl_presentation/vrt_flowchart_dcl_presentation.png)

It attempts to describe the entire process thus far. It only omits things I haven't yet added to this repository. You can also go to [this page](https://www.blocksandarrows.com/editor/lISQXJVO6hjT25OB) if you want to interact with the flowchart.

### Oct 25 Update

I guess I should start by mentioning that the DCL presentation went well! I got some good feedback and suggestions on those dangling research questions in my flowchart. The flowchart also proved to be an effective way to describe what I'm doing. I'll come back to it eventually with updates and so forth. Maybe every couple months, I'll try to translate these journal entries into an updated flowchart.

Anyway, I tried to tackle two things this week:

1) Figuring out how to pull Chron Am data for the Tolnay-Beck Inventory without running my computer for 90+ hours; and,
2) Creating a pipeline for identifying newspapers with digitized content near the locations of lynchings AND over the same timeframe.

On the first task, I've gotten in touch with Dr. Smith at Northeastern. He suggested using the Discovery Cluster/remote server there. It has all the Chron Am data locally. I need to get sponsored access to use this resource at Northeastern, though, so we're filling out the necessary forms. Once approved, I'm hoping he and I can meet to discuss how to use the remote server efficiently. I've done it before, but not with much success... I'm not a command line kinda person. But we'll see, with a little guidance I think I can get it done. Then we'll have the Tolnay-Beck Inventory and any related search results as part of our data.

On the second task, I've had more tangible progress. Here's what I did to identify lynching town newspapers computationally:

I cross-referenced of four datasets:
- Our Seguin & Rigby subset of black victims: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/seguin_rigby_data_black_subset_02.csv](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/seguin_rigby_data_black_subset_02.csv)
- DBpedia's place metadata: [https://github.com/ViralTexts/newspaper-metadata/blob/main/places.csv](https://github.com/ViralTexts/newspaper-metadata/blob/main/places.csv)
- Viral Texts' dbpedia metadata for newspapers: [https://raw.githubusercontent.com/ViralTexts/newspaper-metadata/refs/heads/main/series.csv](https://raw.githubusercontent.com/ViralTexts/newspaper-metadata/refs/heads/main/series.csv)
- Chronicling America's digitized newspaper data: [https://chroniclingamerica.loc.gov/newspapers.txt](https://chroniclingamerica.loc.gov/newspapers.txt)

Using these datasets, I did the following:

1) I cross-referenced VT's dbpedia data with Chron Am's digitized newspaper data. Where there were matches, I added the dbpedia link for location to the digitized newspaper data.
2) I cross-referenced these dbpedia links to the dbpedia places metadata. Where there were matches, I added the dbpedia latitude and longitude data. This gave me the lat/long for each digitized newspaper.
3) After lots of Googling and inquiring with ChatGPT, I focused on what's called the Haversine formula for getting distances between newspaper locations and lynching town locations. This formula calculates the distances between points on a sphere. I'm not an expert on it or anything, but basically this formula calculates the angle between two points and the center of the sphere. Then it multiplies the angle by the size of the sphere to get the distance over the curved surface between the two points. After reading about this method, I adapted code from this Stack Overflow thread: [https://stackoverflow.com/questions/4913349/haversine-formula-in-python-bearing-and-distance-between-two-gps-points](https://stackoverflow.com/questions/4913349/haversine-formula-in-python-bearing-and-distance-between-two-gps-points). This resulted in the function haversine_calculation().
4) Using this function, I iterated over the lats/longs in my newspapers dataframe (the one enriched with location data through the processes in steps 1 and 2) and I iterated over the lats/longs in our Seguin & Rigby Black victim subset. If their locations were within 10 miles of each other, I considered them matches. This 10 mile threshold is just off vibes. It should probably be adjusted based on any information we have about the distance of newspaper circulation and more refined definitions of 'local'. But if there were any matches within 10 miles of each other, I combined the rows in a new dataset called 'nearby_papers_cases'. This resulted in 745 newspapers within 10 miles of where lynching occurred.
5) I still needed to deduce whether these newspapers had digitized pages within the same timeframe as the lynching, though. To do that, I iterated over the First Issue Date and Last Issue Date columns, checking to see if the corresponding lynching date landed within those timeframes. After doing this, I was able to identify about 25 lynching cases that have local papers with digitized coverage.
6) I then mapped the results so it's easier to review them.

There are lots of little processing steps in between these things, but I'm trying to be as clear as possible where it matters. For the full breakdown of these steps in code, you can view this notebook: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/identifying_lynch_town_papers_pipeline.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/identifying_lynch_town_papers_pipeline.ipynb)

To view the mapped results, visit this page: [https://matthewkollmer.com/lynch_town_paper_map.html](https://matthewkollmer.com/lynch_town_paper_map.html)

The red dots are the town locations for lynchings. The black circles are the ten mile radii around local papers. When you hover over either dots or circles, you'll get more information about them, including date, victim, nearest newspaper, etc. 

### Nov 3 Update

I don't have a ton of progress to update this week. Getting remote server access at NEU is a slow process. As it currently stands, I'm waiting for a confirmation email to claim my NEU account. It's been several days of waiting. I sent in a ticket to the NEU IT help desk to try to expedite the process. We'll see. Then it'll be a matter of working with Dr. Smith to know exactly what I'm doing with the remote server... Idk, at this rate, it may have been faster to just segment the Tolnay-Beck data and scrape for a total of 90+ hours runtime. That's a hassle, too, but I'd have most of it done by now. I guess Rome wasn't built in a day, or whatever insufferable aphorism you prefer. I will try to be more patient.

In the interim, I worked on the victim name embedding analysis I had imagined weeks ago. I used BERT to get embeddings from the 'clippings' column and visualized the differences between victim name embeddings with Principal Component Analysis (PCA). These steps can be viewed here: [https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/victim_name_embeddings.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/victim_name_embeddings.ipynb). There's a visualization in that notebook showing the large cluster of Black victims from our Seguin & Rigby data–no surprise there, that these Black victims are treated, for the most part, semantically the same. But I'm wondering what sort of clusters we might observe by comparing Black and white victims. I could segment the data by timeframe, gender, location, all sorts of things, too, to see how victims compare in terms of their semantic differences in our data.

But first I really need to finish curating the data. I think we've got lots of possibilities here. We have good steps toward analysis. The thing to do now is to finish compiling the data.

### Nov 9 Update

I have no code updates worth pushing but I've been working on my ADHO proposal, NEU remote server access, and other forward-thinking tasks.

I'm hoping to have more on my ADHO proposal by tomorrow or Monday. But on the NEU server access, I've been going back and forth in emails for each step with the IT help desk. I think I'm on the last hurdle for getting access to my account, but we'll see. I'm waiting for them to follow up on reinstating my ability to use their two-factor authentication app which is required to claim the sponsored account.

In the meantime, I've been thinking about how to best present this whole project and make it easily replicable for other scholars. Of course, these entries and all my steps are laid out in the order I completed them, but even I'm beginning to lose track of the strands of work that have gone into this exploration. To make things easier in the future, and to maybe create a more presentable repository, I'm thinking of redrafting these steps in more streamlined order. For example, I might as well combine the Seguin & Rigby & Tolnay-Beck datasets into one file. I've also been cleaning and enriching the data across numerous notebooks–some with subsets, others to enrich with lat/long data, others to add columns and so forth. I think it'd be easier to replicate and demonstrate this work if I essentially revise and consolidate the code. 

If I do this, I'll archive this repository, too. I think there's a lot of intellectual value in the documentation here. It's revealing how this project unfolded in chronological order. But that doesn't necessarily mean it will be the clearest way to showcase the work or allow others to adapt or replicate it. If I do this, I'll also try to rewrite the scraping parts to account for the upcoming changes to the Chronicling America API. That way, I'm also working ahead on future challenges for the project.

### Dec 6 Update

It's been far too long since my last update. Apologies to you, process journal. I didn't mean to neglect these entries. I've been plugging away at tasks but forgetting to record them. It's time for me to catch up.

I think the most important news is that we were able to put together a panel proposal for the ADHO conference. Hooray! Our panel includes VRT folks and a group of PhD students from the iSchool working on a similar NLP project with ephemeral materials. Fingers crossed we're admitted to the conference. I'm not gonna lie, I have high hopes. I won't post all our abstracts here, but quite frankly, I think they're all very good. Here's what I wrote:

“US Lynching Victims: Text-Mining Digital Newspaper Archives for Lynching Reports and Victim Names”

There is a long history of using data-driven methods to study lynchings in the United States. In the 1880s, the Chicago Daily Tribune published lynching reports from across the nation. Using their data, Ida B. Wells-Barnett conducted some of the first major analyses, revealing how white terrorism increased wherever Black citizens gained political power (Wells-Barnett, 1895). Decades later, the NAACP began tabulating its own data and publishing sweeping reports (NAACP, 1919). So did Monroe Work at the Tuskegee Institute. His reports effectively applied statistical data to dispel white justifications for lynchings (McMurry, 1980).

My paper draws on this history of scholarship and activism. Using victim names recorded in two recent lynching records–the Tolnay-Beck Inventory (1995) and the Seguin & Rigby dataset (2019)–I have text-mined the Chronicling America archive and curated a new dataset of over 10,000 lynching reports from 1865 to 1921. My data includes references to roughly 4,000 lynching victims, providing insight into how they were represented at a national scale via the widespread reports of their murders. This paper aims to provide clear documentation of the production of this dataset and to advocate for its ethical use in studying the painful legacy of lynchings in the United States. To that end, I also demonstrate some effective use cases, including geospatially mapping lynching reports, comparing sentiment toward victims via BERT embeddings, and conducting word frequency analyses to understand how reporting changed over time.

References:

- McMurry, L. O. (1980). A Black Intellectual in the New South: Monroe Nathan Work, 1866-1945. Phylon (1960-), 41(4), 333. https://doi.org/10.2307/274858
- Seguin, C., & Rigby, D. (2019). National Crimes: A New National Data Set of Lynchings in the United States, 1883 to 1941. Socius: Sociological Research for a Dynamic World, 5, 2378023119841780. https://doi.org/10.1177/2378023119841780
- Thirty Years of Lynching in the United States, 1889-1918. (1919). NAACP; Internet Archive. https://archive.org/details/thirtyyearsoflyn00nati
- Tolnay, S. E., & Beck, E. M. (1995). A Festival of Violence: An Analysis of Southern Lynchings, 1882 - 1930. University of Illinois Press.
- Wells-Barnett, I. B. (1895). The Red Record: Tabulated Statistics and Alleged Causes of Lynching in the United States (Northern Illinois University). Digital Library. Retrieved November 18, 2024, from https://digital.lib.niu.edu/islandora/object/niu-gildedage%3A23615

We'll see what the reviewers think. Of course, I think it's a worthwhile topic. I think the essential logic of the computational methods is not particularly groundbreaking. It's a simple keyword-focused information retrieval task. But it works, that's what really matters. The analysis of the subsequent data may yield more innovative methods, though. And most of all, I think there's important work to be done in studying the historical travesties of lynchings with real people named, emphasized. This work is at least providing new ways to do that.

Anyway, that's one major hurdle for the project complete. I've also been working on a polished repository which I mentioned in my previous entries. It can be viewed here [https://github.com/MatthewKollmer/us-lynching-victims](https://github.com/MatthewKollmer/us-lynching-victims). As I stated previously, the goal of this new repository is to support step-by-step replication and presentation of the process(es) of building and analyzing this dataset. In some ways, it feels like I'm creating a rough draft/outline for an article about the dataset. I'm compartmentalizing the steps and labelling them in code notebooks ([01_unify_data_sources](https://github.com/MatthewKollmer/us-lynching-victims/blob/main/01_unify_data_sources.ipynb), [02_pull_search_results](https://github.com/MatthewKollmer/us-lynching-victims/blob/main/02_pull_search_results.ipynb), etc.). When it's fully outlined and presented, I'll basically just need to write an overview of the steps and results for a scholarly audience who may use the factual claims derived from my data analyses, or may use the data itself for their own analyses, to build on my work. That's the goal of this repository. It's been straightforward thus far since it's only representing the initial data curation steps. Once I get to the analysis steps, I may need to pause and consider best orders of presentation. But those challenges are on my radar.

In this new repository, you may notice I've gone ahead and started scraping the results from Chronicling America. Yes, it's true. I'm still working on getting the NEU remote server configured, but in the interim, I've chunked the search results and I'm slowly scraping Chron Am. I'm over halfway done already. This will result in a dataset of about 450,000 search hits. Once complete, I'll take code from the notebooks in this repository to refine the data, essentially repeating my steps with minor alterations to create a dataset of victim references with far fewer false positives. So, that's a solid development.

And that's pretty much everything I've been up to over the past few weeks. There's only one week left in the semester now, but I'll try to post again at least once before we take off.


### Dec 19 Update

Here is a link to the Chron Am raw search results data for victims in both Seguin & Rigby and Tolnay-Beck: [https://uofi.box.com/s/dqf9ac3in29hfz0gbrbleqzvkv13w2hs](https://uofi.box.com/s/dqf9ac3in29hfz0gbrbleqzvkv13w2hs)

Post-script on Jan 7:

Oops! Forgot to provide more context on Dec 19th. I was in a hurry, trying to finish up for the semester. But good news: on December 19th I finished scraping Chron Am for all victim names from Seguin & Rigby and Tolnay-Beck. The link above takes you to the unfiltered data from the scraped search results. It's a zip file called chron_am_search_hits.zip. It contains 3,994 csv files, one per victim, broken into 96 chunks to make the scraping more manageable. In total, it's 453,050 pages of digitized newspaper text!

### Jan 7 Update

Just a little progress report. I've been doing things over break when I've been free and motivated. I added [03_scrape_search_results.ipynb](https://github.com/MatthewKollmer/us-lynching-victims/blob/main/03_scrape_search_results.ipynb) to my polished US Lynching Victims repository. It shows how I took the large scraping task and broke it into manageable chunks in order to build the new dataset of search hits. Since then, I've been working on 04_preprocess_pages.ipynb. It will contain all the steps I'm taking to filter the search results for instances of racial violence. I'll push this notebook in the coming weeks, too, but it's taking a while because I'm trying to optimize everything in this iteration.

That optimization has been focused on my fix_names() function to start. It's a tedious little step, but I'm finding it to be hugely important. The thing is, I've looked at enough of the data to know that Chron Am uses a fuzzy match search. This means it identifies not just pages with the exact word or phrase from the search, but rather all pages with words or phrases that are similar based on certain parameters (no doubt to account for OCR errors). But in turn, this means that lots of the 453,050 scraped pages contain search hits that are just a little off, making further steps, like getting clippings, more difficult. In my first iterations, I wrote a fairly simple fix_names() function, but this time, I've worked harder at it, and come up with this version: [fix_names_demo.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/fix_names_demo.ipynb). That demo notebook explains it with some examples in code. I'm satisfied with it now. I think it will improve the final dataset by a large margin.

Next up is optimizing the clippings and the signal words. The clippings should be easy, the signal words not so much. More soon–

### Jan 29 Update

Lot's to update, but it's 9:20pm right now, and I am tired, so I'm keeping it brief. I've been successful in preprocessing the 453,050 scraped pages. I've corrected some OCR errors, extracted clippings, counted violence signal words, and counted race signal words. This improved dataset can be downloaded from this Box folder: [https://uofi.app.box.com/folder/305076534062](https://uofi.app.box.com/folder/305076534062).

I'll try to write more tomorrow!

### Jan 30 Update

As promised, I'm elaborating on my progress over the past two weeks. In that time, I've been working extensively on several NLP preprocessing steps. These steps enrich the data as outlined briefly in yesterday's entry. Code for these steps can be viewed here: [https://github.com/MatthewKollmer/us-lynching-victims/blob/main/04_enrich_dataset.ipynb](https://github.com/MatthewKollmer/us-lynching-victims/blob/main/04_enrich_dataset.ipynb). This notebook does the following:

- reorganizes the directory (removing subdirectory chunks)
- fixes some OCR issues to identify more references to victims
- creates 'clippings' of the text around references to victims
- counts signal words (i.e., words related to violence and race) that appear near the victim's name

I'm satisfied with it, for sure. At every part–fixing names with OCR errors, getting clippings, counting signal words–I've essentially refined the whole process. I'm confident it has improved our dataset. It may not seem very different from previous iterations of the data curation process, but I think it's made everything more robust, so that's good.

Now I've got to pair some of this data with specific cases, as we discussed in the last VRT meeting. I'll be working on that for the next 24 hours. I also plan to review a subset of this data and create a training set for classification. I've got ideas there, but before starting, I'll run it by the group tomorrow and see what people think.

#### Data relevant to known cases:

- 1873 Colfax Massacre ([see Wikipedia](https://en.wikipedia.org/wiki/Colfax_massacre), [Colfax Memorial](https://www.colfaxmemorial.org/)). In our data, reports related to the death of Alexander Tillman seem to be related (see [alexander_tillman.csv](https://uofi.app.box.com/file/1763423914859)).
  
- If we want to look at any/all instances from North Carolina, the following csv files are probably our best bets: charles_winters.csv, thos_bradley.csv, john_hirst.csv, john_middleton.csv, archie_beebe.csv, dan_morrow.csv, mary_lomax.csv, amos_jones.csv, daniel_smith.csv, wright_woods.csv, john_miller.csv, robert_grady.csv, william_puryear.csv, wyatt_outlaw.csv, robin_jacobs.csv, silas_weston.csv, david_weston.csv, theodosia_weston.csv, tom_lowry.csv, steve_lowery.csv, jule_davidson.csv, shadrack_hester.csv, john_brodie.csv, edmund_davis.csv, j_lindsey.csv, charles_campbell.csv, lawrence_white.csv, george_johnson.csv, erwin_mccullough.csv, charles_smith.csv, bud_mebane.csv, lee_tyson.csv, john_pattishill.csv, jerry_finch.csv, harriet_finch.csv, john_boggan.csv, alfred_long.csv, eugene_hairston.csv, benjamin_hart.csv, john_humphreys.csv, john_carson.csv, sherman_farrior.csv, john_tanner.csv, henry_tanner.csv, alonzo_smith.csv, jack_blount.csv, matthew_blount.csv, david_boone.csv, john_sigmund.csv, kinch_freeman.csv, mack_best.csv, hezekiah_rankin.csv, joe_barco.csv, william_burnett.csv, duncan_mcphatter.csv, lyman_purdie.csv, robert_chambers.csv, bob_brackett.csv, jim_greene.csv, manly_mccauley.csv, joseph_kiser.csv, thomas_johnson.csv, henry_jones.csv, lewis_patrick.csv, george_ratliffe.csv, george_rittle.csv, avery_mills.csv, luke_hough.csv, peter_mitchell.csv, jim_bailey.csv, harrison_gillespie.csv, james_gillespie.csv, thomas_jones.csv, james_walker.csv, manna_ponton.csv, john_osborne.csv, dick_whitehead.csv, john_moore.csv, jack_dillingham.csv, john_gillespie.csv, nease_gillespie.csv, joseph_mcneely.csv, james_wilson.csv, bessie_perry.csv, joseph_black.csv, john_richards.csv, lazarus_rouse.csv, george_taylor.csv, peter_bazemore.csv, george_johnson.csv, walter_elliott.csv, powell_green.csv, john_daniels.csv, john_jeffress.csv, edward_roach.csv, ernest_daniels.csv, jerome_whitfield.csv, alfred_williams.csv, plummer_bullock.csv

I won't hyperlink to every one of those files here, but they can be searched for easily if you download the whole dataset or by searching in the Box folder: [https://uofi.app.box.com/folder/305076534062](https://uofi.app.box.com/folder/305076534062)

- the Click Mitchell case may be worth investigating: [https://uofi.app.box.com/file/1763432104464](https://uofi.app.box.com/file/1763432104464). Occurred in Urbana, Ohio, 1897. There are a lot of printings about it, seems to have been a widely reported event. It also seems to have resulted in some anti-lynching legislation. This I have gathered from reading the reports in the data.
  
- I keep returning to the Aaron Thomas case: [https://uofi.app.box.com/file/1763432104464](https://uofi.app.box.com/file/1763432104464). Trigger warning on this one, though. It was widely reported (in brutal detail). The case involves the lynching of the Thomas family. Aaron Thomas was just a child. It may be worthwhile to look at the political leanings of the newspapers in this case. Are they including the details to highlight the horrid nature of lynching? Or are they publishing the gruesome details merely to intrigue? I don't really know.

### Feb 4 Update

All right, I think I've devised a plan to create a training dataset in order to fine-tune BERT to classify our newspaper clippings as related to lynchings or not.

Here's the big idea:

1) First I created a random sample of 1,000 newspaper clippings (see [05_random_sample.ipynb](https://github.com/MatthewKollmer/us-lynching-victims/blob/main/05_random_sample.ipynb))
2) Then I created ten separate Google Colab notebooks to divvy up the labelling work (see [VRT labelling notebooks](https://drive.google.com/drive/folders/1pzUHEMj8bZCEDgbt6m3L648ck7f8Kbtv))*
3) Now I'll just need to run each one of those notebooks to label the data.
4) Once complete, I'll use this training data to fine-tune BERT

Step number 2 needs further explanation.* So, basically, these Colab notebooks each handle 100 clippings. That's admittedly a lot to do in one sitting, but based on my prior work in [case_match_review.ipynb](https://github.com/MatthewKollmer/works-in-progress/blob/main/vrt_work/us-lynching-victims/case_match_review.ipynb), I don't think it will be unreasonable. I imagine each one to take maybe an hour or so. How it works is that each notebook prompts the reviewer to classify clippings one-by-one. The classification options are 'yes' (it is entirely about a lynching), 'no' (it is not about a lynching), 'partial' (some of it's about lynching, but not all), or 'unknown' (it's in a language I do not understand and/or the OCR is so bad it can't be read). Once the reviewer finishes, they just need to run the last bit of code to save their labels as a .csv file, and then email this .csv file to me. From there, I'll concatenate these labels and turn them back into one file.

Of course, I'm planning to do a lot of this labelling myself, but in the last meeting it seemed like I would get some help, so I hope this system works for folks. We shall see. I'll pitch it to the group on Friday. In the interim, I'm going to work on the code for fine-tuning and running BERT.


### Feb 12 Update

Just a lil' update. Half of the training data has been labelled (500 out of a 1,000 clippings). So far, 92 clippings contain text about lynchings (18.4%). We'll see if that holds as we label more data, but assuming it does, I can't help but hypothesize about the larger breakdown of our dataset.

For one, after running the fix_names() function and all the other preprocessing steps, about 356,000 of our roughly 450,000 scraped pages contain clippings. Remember, these clippings were pulled from pages where the victim's name was spelled correctly, so this also means about 356,000 of our 450,000 (roughly 80%) are usable pages for victim name analysis.

Then, assuming our training data is representative of the rest of the clippings data: this means we have about 18% of those 356,000 pages (roughly 64,000) that actually contain references to lynchings.

That's a good amount, but what to do with the other ~390,000 pages we've scraped? While a lot of it is false positives, I actually think we should maintain these pages for a variety of reasons. Here's a brief list that have occurred to me thus far:

1) Sometimes, the victim's name is mentioned and their alleged crimes are mentioned, but there's nothing said about their lynching. In these cases, they're getting labelled 'no' (as not containing reference to lynchings). Yet they may be important in other analyses. For example, you could look at the data diachronically, and assess whether victims being named as criminals in local or regional papers ever preceded their lynchings. If this were the case at a large scale, it could support arguments about the relationship between newspaper reporting and the fomenting of racial violence.
2) A not-insignificant portion of the labelled data seems to be in languages other than English. In these cases, we're labelling them 'unknown', but they very well could contain reports of racial violence. Maintaining these cases may be helpful if anyone wanted to analyze how newspapers from different language communities reported on instances of racial violence.
3) So far, I haven't tried to analyze the roughly 96,000 scraped pages that do not have clippings (i.e., those pages where my fix_names() function does not correct the fuzzy-matched hits, and so they don't have victim names spelled correctly). Perhaps more reports of lynchings could be found there, if I can hone my fix_names() function even further.

That's what I've been thinking about this week. I'm eager to finish our training data, though, and to use it to classify the rest of the clippings. If it works, I think we're at the point where we can move ahead to pure data analysis (as opposed to data curation). That'll be cool. Also, coincidentally, this week I re-read Bamman et al.'s ["On Classification with Large Language Models in Cultural Analytics"](https://arxiv.org/pdf/2410.12029) and it proved helpful in thinking about our classication task. When I write up an article about building this dataset, I'll have to explain how our use of BERT and supervised modeling fits into the common "search" & "replacing human labelling at scale" purposes of classification in cultural analytics. Our work here is also an example of classifying novel categories, making supervised modeling the better approach than using LLMs with zero-shot or few-shot prompting.

### March 4 Update

I forgot to update last week. Whoops! I'll just report progress quickly and lay out what I think I'll have done by the end of this week.

If you've been reading along, you'll know that I've been trying to get a firm grip on classification to streamline our labelling of all these victim name clusters. Last week showed progress in that regard, but also challenges. Using the training data that Abraham, Amanda, and I labelled, I tried classifying with TfidF and logistic regression, BOW and logistic regression, and BERT. Of these attempts, TfidF and BOW were especially inaccurate. On identifying clippings with reference to lynchings, TfidF had F1 scores of .31 and .07 on 'yes' and 'partial' labels, respectively. BoW was slightly better, but still horrible. BERT did the best but still only had F1 scores around .7 with especially poor recall on the 'yes' and 'partial' labels.

In last Friday's meeting, I asked Cordell and Blankenship about how to make adjustments. One suggestion was to turn it into a binary classification problem ('yes' for any reference to lynchings and 'no' for no reference to lynchings) as opposed to a four category labelling question. I tried that over the weekend, and good news: it improved results! As a binary classification, BERT improved to .87 recall and F1. That's approaching where I'd like to be. The goal is to be better than signal word labelling, which, when Blankenship and I first hand-reviewed it, resulted in roughly 90% accuracy.

On that note, I also looked closer at signal word labelling and found that 'yes' and 'partial' labels had a median of about 4 violence word signals and 1 racist word signals whereas 'no' and 'unknown' had medians of 0. So, there's definitely something there to that method. But if I can get BERT to be better at this classification problem, I think I'd rather use that so we're not so limited by our signals.

So, how to get BERT above the 90% threshold? That's the problem for this week. Here's what I'm gonna try:

- down-sampling 'no' labels (another Cordell & Blankenship suggestion)
- increasing the size of the training dataset (more hand-labelling)
- spending more time reading about BERT's variables (text length, number of epochs, etc.) and testing results with variable changes

I hope to report back with some demonstrable code soon!
