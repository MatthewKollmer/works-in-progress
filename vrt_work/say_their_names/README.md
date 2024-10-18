# About

These contents are the preliminary code and data used to identify racial violence victims in historical newspapers. The code is my own, but full disclaimer: I often collaborate with ChatGPT to make corrections if I get errors and I ask it questions about Python libraries I'm not familiar with.

Also, larger files are not hosted here. They can be found in this Box folder: 

[https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud](https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud)

If you don't have access to Box folders and you'd like the larger files, please email me: kollmer2@illinois.edu. I will send them to you.

### Sept 3 Update

Here's a step-by-step breakdown of what I've done so far:

1) Using Seguin & Rigby's lynching dataset ([see here](https://journals.sagepub.com/doi/pdf/10.1177/2378023119841780) and [here](https://archive.ciser.cornell.edu/studies/2833/data-and-documentation)), I extracted names of Black victims between 1883 and 1921, and cities where their murders occurred. If full names or cities were missing, I didn't include them in my subset of the data. This resulted in about 450 instances with victims and places named. See my [preprocessing notebook](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/preprocessing.ipynb) for this code.
2) I put the names, cities, and years of occurrence into a pipeline that scrapes Chronicling America search results. This scraping pipeline identifies any pages from the year of occurrence where the victim name and city appears within 100 words of each other. Then it scrapes the newspaper content of all those pages. The scraped contents were saved to separate csv files (separated by victim names). This resulted in about 350 victim clusters and a total of about 19,000 digitized pages. See my [scraping notebook](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/build_newspaper_dataset.ipynb) for this code.
3) Then I did a little bit of post-processing (more to be done yet). I corrected minor OCR errors (added spaces where they were missing between victim names) and created clippings of the newspapers around instances of victim names (the 100 words before and after victim names). See my [post-processing notebook](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/postprocessing.ipynb) for this code.
4) Look to the lynch_clusters directory in my [Box folder](https://uofi.app.box.com/folder/283228321987?s=hhekbeupm9sgkh8nb3dbo7ial4xpioud) to find the results–about 350 .csv files titled after each victim's name, with pages where they–and the city of their murder–was named.

### Sept 10 Update

Over this week, I enriched the lynch_clusters with geolocation data derived from the Viral Texts' [place metadata](https://github.com/ViralTexts/newspaper-metadata) for newspapers. I also used some Python libraries to build maps of the reprints. The code for these activities can be viewed here: [https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/geolocate_reprints.ipynb](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/geolocate_reprints.ipynb).

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

All these steps can be viewed in this messy Jupyter notebook: [https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/build_refine_dataset.ipynb](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/build_refine_dataset.ipynb)

### Sept 19 Update

In the last day, I've tried to bring it all together. I've added more columns to the new, refined dataset (columns for 'newspaper' and 'reprint_date'). I also remade the maps with the refined dataset. I also went ahead and created a web page to view the map demo. It is password protected. Contact me if you need the password. But the page can be viewed here: [https://matthewkollmer.com/vrt-say-their-names-demo/](https://matthewkollmer.com/vrt-say-their-names-demo/).

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

Anyway, alongside Avery, I also reviewed the data to see how accurately the clippings are capturing instances of racial violence. I did this in a pseudo-programmatic way. Basically, I built a loop to prompt me to review each row and label the clipping as either totally about racial violence, partially about racial violence, not about racial violence at all, or unknown. This process can be reviewed here: [https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/case_match_review.ipynb](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/case_match_review.ipynb). I looked at 171 rows and four csv files. A little over 90% of them were totally or partially about their respective racial violence events. So, that's encouraging–it means that the refined dataset I've put together probably isn't cluttered with false positives. But we'll see how it went for Avery, too. And of course, we should probably review far more of the data. There are something like 10,000 rows. A couple hundred of them is still a small sample size.

Oh, almost forgot–a large part of my week was also preoccupied with reviewing Chronicling America's interface and API changes, which are set to be implemented at the end of this year... Ugh, this is a bit of a nightmare. Once they change the API, most of my code will be broken. I'm going to need to change my scraping approaches drastically. I'm looking more closely at downloading their batches of files rather than scraping by URL. It's going to be a different process altogether, but nothing to be done about it. Must plan accordingly.

### Oct 1 Update

Updates: I decided to forge ahead on the local/regional newspapers idea–that is, the plan to identify towns where lynchings occurred and where nearby local or regional newspapers are 1)digitized and 2) show coverage around the same timeframe. Toward those ends, I put together this notebook: [https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/identify_build_lynching_town_datasets.ipynb](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/identify_build_lynching_town_datasets.ipynb). Basically, this notebook contains four general steps:

1) finding localities (city, state) in our data where lynchings occurred
2) Geolocating these lynching towns
3) identify lynching towns with digitized newspapers
4) building lynching town subsets

These steps were successful, but I ran into some surprises along the way. Firstly, after reviewing our Black subset of Seguin & Rigby's data, I discovered it contains very few lynchings from the majority of the Southern states?! This surprised me greatly. There are lots of instances from Texas, but not Louisiana, Mississippi, Georgia, Alabama, or Florida. I need to investigate why. I touch on one hypothesis in the notebook (is it because I removed instances without named victims?), but I haven't investigated this anomaly further.

Secondly, I was surprised by how difficult it was to find towns where lynchings occurred and where there was relevant available newspaper data. Now, this part of the process is ongoing, but I had to review by hand the Chron Am map of newspapers ([see here](https://loc.maps.arcgis.com/apps/instant/media/index.html?appid=3c6a392554d545bdb1c083348ef56458&center=-97.5126;39.6376&level=3)) and my own map of lynchings. This process really revealed the limitations of the Chron Am data. As I say in my notebook, "the limitations of Chron Am's data reveals itself here in ways you don't realize looking at the data as rows or lists. [...] the map makes you see the gaps in spaces where surely there were newspapers, but they are not digitized or available." 

Still, I was able to find a few overlapping spaces and cases. I was also able build subsets of these newspapers by scraping Chron Am. Those subsets can be downloaded here: [https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/lynching_town_newspapers.zip](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/lynching_town_newspapers.zip). They are:

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

I'm overdue for an entry here. In the past two weeks, I've been busy on a few fronts with the project. It's growing tendrils, this thing. First, there's the lynching town paper analysis and the pipeline for identifying more lynching town papers. I've written some preliminary, exploratory code for studying lynching town papers: [lynch_town_paper_analysis.ipynb](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/lynch_town_paper_analysis.ipynb). This notebook contains written considerations and visualizations, but to summarize briefly: I've tried embeddings, word frequency stuff, and topic modeling, but I need to anchor my analyses more thoughtfully. And as for a pipeline for identifying more lynching town papers, I'm on it, but I haven't finished a notebook worth uploading quite yet.

Part of my delay in that regard is the tendril with the largest potential: the additions of the Tolnay-Beck Inventory. You may remember from  [identify_build_lynching_town_datasets.ipynb](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/identify_build_lynching_town_datasets.ipynb) that I noticed a glaring geospatial gap in our data–there were no recorded lynchings from the Deep South. Well, it turns out that was because Seguin/Rigby's data implicitly tried to expand lynching data to a national scale. They did this because the Tolnay-Beck Inventory–an earlier and widely regarded dataset of lynchings–had recorded only lynchings from the Deep South. In turn, I had to find the Tolnay-Beck Inventory. The PIs for VRT were gracious enough to send me a copy they had from 2016. I also reached out to Dr. Amy Bailey at UIC who maintains the dataset today. She sent me the latest version (last updated 2022).

Anyway, this is a tendril with a lot of potential because the Tolnay-Beck Inventory contains about 3,500 more victims relevant to this project. That's ten times as many as the Seguin/Rigby data... I've done some first steps in preparing this data for the scraping process (see [tolnay_beck_additions.ipynb](https://github.com/MatthewKollmer/messing-around/blob/main/vrt_work/say_their_names/tolnay_beck_additions.ipynb)). However, it's gonna take some more planning before I scrape all those results. For reference, scraping Chron Am for the Seguin/Rigby data took about nine hours of runtime. If I have to scrape ten times that amount, it'll take about 90 hours. If possible, I'd like to find a workaround. Otherwise, I'll have to scrape the results in batches over the course of a couple weeks.

We'll see. Tomorrow's meeting will hopefully give me more insight. But for the rest of this week, I'll prepare for a presentation on this project in the Data Cultures Lab (an informal DH lab at UIUC). I'll also see if I can finish the lynching town paper identification pipeline. Lots to do! Tendrils, I say. There's also reviewing the results more thoroughly, improving the signal word lists/lexicon, and other stuff. I think this Friday's presentation will be a good opportunity to get all these things in order.

### Oct 17 Update

Tomorrow I have the presentation for the Data Cultures Lab. As part of my preparation, I put together this flowchart:

![Flowchart for DCL Presentation](https://github.com/MatthewKollmer/messing-around/raw/main/vrt_work/say_their_names/dcl_presentation/vrt_flowchart_dcl_presentation.png)

It attempts to describe the entire process thus far. It only omits things I haven't yet added to this repository. You can also go to [this page](https://www.blocksandarrows.com/editor/lISQXJVO6hjT25OB) if you want to interact with the flowchart.
