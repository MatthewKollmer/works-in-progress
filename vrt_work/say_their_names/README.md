# About

These contents are the preliminary code and data used to identify racial violence victims in historical newspapers.

Larger files are not hosted here. They can be found in this Box folder: 

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
