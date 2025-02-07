<template>
  <div style=" text-align: justify;">
    <div class="col-12" ref="introduction" style="margin-right: auto;
    margin-left: auto;padding: 50px 15px 75px;max-width: 850px;">
      <div style="text-align: center; font-style: italic;font-weight: bold">
      Is it possible to analyze politics starting exclusively from the politicians' quotations? <br>
      Is it possible to extract significant information that allows us to extract meaningful insights about American politics?</div><br><br>
      Follow us along on our journey where we apply data analysis techniques to better understand political trends of the past 5 years.<br><br>
      The ensemble of all quotations of the members of a party is an excellent representation of the ideas and values of that party. In fact, these quotations represent the very vision of the political party. Starting from this reflection, let's analyze American politics starting from the quotes found in the Quotebank dataset.
      In Quotebank there are more than 7 million quotations of American politicians, among them, more than 6 million can be associated to 15000 politicians affiliated either to the Democratic Party or the Republican Party. <br><br>

      We have trained a machine learning model to predict the political affiliation of the speaker of a quote. With a rather simple model we have managed to achieve a 75% accuracy, which is quite a surprising result! This goes to show how polarized American politics have become nowadays. Both parties often have very clearly opposite opinions of topics such as climate change or abortion. <br><br>

      We will also use our model and data to explore how a politician's opinions align with their party, perform time series analysis to analyse their evolution over time, analyse the polarization of parties on different topics and more! <br><br>

      So hop on and come see the surprising power of data analysis in our modern day and age!

      <!-- As we will show later some there are multiple topics that show a clear polarization between the two political parties
      We have trained a machine learning model to classify these quotes to the corresponding political party. Our model associates a score that varies from 0 to 1 to each quotation representing the probability that the quote is said by a democrat. -->
    </div>
    <!--<Introduction ref="introduction"/>-->
    <Capitolo1 id="exploration"/>
    <div class="col-12" style="margin-right: auto;
    margin-left: auto;padding: 50px 15px 0px;max-width: 850px;">
      <h4>The model in numbers</h4>
      The most important question: Can the model understand something? And how good are its predictions?<br>
      On the test set, our model needs 8 quotations to obtain an accuracy greater than 80% on the prediction. In addition, if we try to classify a politician, the accuracy is over 98%.<br>
      For a single quote, the accuracy exceeds 75%. This value may seem low if you don't consider the fact that the data is noisy. As one might expect, although the majority of the quotes of a politician have to do with politics, not all quotes do. Additionally, some of the quotes might not have enough words for the model to truly understand the context.
      <br><br>
      <h4>The model in practice</h4>
      First things first, let's see how some familiar faces are positioned in our model predictions (mouse over the graph to see names associated to each curve):<br><br>
      <div style="width: 100%;margin: 0 auto;">
      <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/19.embed?show_link=false" height="525" width="100%"></iframe>
      </div>
      <br><br>
      We immediately see that the model manages to separate politicians into the two categories. What's even more interesting is that the score is also correlated with how strongly a politician's opinions veer towards on party or another: the more radical politicians, such as Bernie Sanders, have average scores closer to the extremes.<br>
      Now we have an interpretation for this political score that we can keep in mind for future analyses. You can notice the large zig-zag on the graph: that's the time-series of Alexandria Ocasio-Cortez. In the first few years she has a high variance and from 2018 her curve stabilizes and joins the rest of the democrats. In fact, she only became a prominent American politician in 2018, before that she has much fewer quotations, which perfectly explains the high variance before 2018 and that how after that she indeed joins her party trends.<br><br>

      <h4>About misclassification and robustness</h4>
      So far we have achieved a decent per-quote accuracy and seen how aggregating the quotes scores for a single politician allows us to accurately classify popular politicians. <br><br>

      Naturally, the next question we ask ourselves is: what are the restrictions we have to impose to make a robust prediction of which party a politician is affiliated to? <br><br>

      First we'll look into how often we misclassify speakers. Let's plot how many speakers we misclassify and see if that is correlated with the number of quotes a speaker has attributed to them.

      <div style="margin: 25px 0 25px 0;text-align: center">
        <img  style="margin: auto" width="65%" :src="require('@/assets/images_nicky/img_3.jpeg')" alt="distribution"/>
      </div>
      Taking a closer look, we realize that 13% of our misclassified politicians have only one quote associated. Here's a graph of speaker predictions with only 1 quote. (you can mouse over the points to read the associated quotation)<br>
      <div style="width: 100%;margin: 0 auto;">
        <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/25.embed?showlink=false" height="525" width="100%"></iframe>
      </div>
      Among these quotes, we can notice 4 types of quotes:
      <ul style="padding-left: 50px">
        <li>Quotes that lack context (e.g. “ “). Note that some quotes are empty because our preprocessing removes stop words: this means that the quote must have contained only insignificant words</li>
        <li>Quotes that have nothing to do with politics (e.g. “Bob earned his spurs“)</li>
        <li>Quotes that should be correctly classified but the model failed to do so</li>
      </ul>
      <br>

      The residual 87% of misclassified quotes have more than one quote. To better visualize the issue, here's a plot of the average political score of each misclassified speaker with 95% confidence intervals using bootstrapping:
      <div style="width: 100%;margin: 0 auto;">
        <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/29.embed?showlink=false" height="1000" width="100%"></iframe>
      </div>
      Here we see that many of the confidence fall on both sides of the graph. Clearly, the variance on the predictions of many of these speakers is too high. That means that we need to set a minimum number of quotes to make a robust prediction of a speaker.
      <br>
      To decide where to set the cutoff, we calculate the accuracy of a politician political party classification as a function of the number of quotes we have associated to that politician.<br><br>
      <div style="width: 100%;margin: 0 auto;">
        <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/34.embed?showlink=false" height="525" width="100%"></iframe>
      </div>
      By setting  the minimum required accuracy at 92.5%, we found out that the minimum number of quotes a speaker needs for the model, to make a robust prediction, is 4.<br><br>

      We repete the same process but with respect to the length of a quote instead of the number of quotes per-speaker. The following plot shows that, in order to have a satisfing accuracy, a quotation must contain at least four non-stop words.
      <div style="width: 100%;margin: 0 auto;">
      <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/36.embed?showlink=false" height="525" width="100%"></iframe>
      </div>
      <h4>Translating political scores into words</h4>

      Next we'll visualize the distribution of the scores of the quotes by separating the two classes.
      <div style="margin: 25px 0 25px 0;text-align: center">
      <img  width="80%" :src="require('@/assets/graphs/img_distribution.png')" alt="distribution"/>
      </div>

      We can see that there are 4 distinct intervals on the graph. From the left, an exclusively Republican side, then a predominantly republican side, a predominantly democratic one and finally an exclusively democratic zone. <br>
      Starting from this idea, we took an interest in trending words by section and the result is very interesting and significant.
      First, let's look at the trending words that are specific to each interval:
      we immediately notice that they are most present for the exclusive republican and democratic intervals. Specifically in the exclusively democratic zone we find the following words:
      community, health, country, city, new. For the exclusively republican side we find instead: american, government, trump and states.
      With the exception of the word 'new' these words are extremely significant for the vision of parties. In facts, the words are really different from each other, and they are really important to identify the differences between the four zones.
      Republican's have common words related to the homeland and patriotism, while Democrats have common words related to community, people and health.

      <div style="margin: 25px 0 25px 0;text-align: center">
      <img width="80%" :src="require('@/assets/graphs/img_1(unique).png')" alt="words(unique)"/>
      </div>

      Let's now look at the words that are shared by several intervals. Here too we see some trends. Words that concern "America" are more on the Republican side of the spectrum, whereas words that concern "People" are more on the Democratic side.

      <div style="margin: 25px 0 25px 0;text-align: center">
      <img width="80%" :src="require('@/assets/graphs/img_1(shared).png')" alt="words(shared)"/>
      </div>

      So, our model is able to accurately classify the political party to which a politician belongs and 4 quotations of sufficient length (4 meaningful words) are enough to accurately predict a speaker's political party.
      Thus, if a politician has many associated quotes
      we are able to group them into small groups of quotes, close in time, and create a time-series that represents the political vision of that politician.
      The quotations themselfs, with their corresponding scores, are already time-series.  We resample the time series, for example with a monthly frequency,
      in order to decrease it's political score variance. This time series will be really useful for extracting meaningful insights about American politics. (See next chapter)<br><br>
      Now that we have seen the models strengths and particularities, let's look at the the distribution of the political scores of all the politician on our dataset.

    </div>
    <div class="col-12" style="margin-right: auto;
    margin-left: auto;padding: 10px 0 0 0;max-width: 1050px;">
        <img width="1162" height="450" :src="require('@/assets/images_nicky/mados.jpeg')" alt="distribuition_scores"/>
      </div>
    <div class="col-12" style="margin-right: auto;
    margin-left: auto;padding: 10px 15px 75px;max-width: 850px;">
      Finally lets show the political score of some of the most famous american politicians:
      <div style="width: 100%;margin: 0 auto;">
        <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/42.embed?showlink=false" height="525" width="100%"></iframe>
      </div>
    </div>
    <UseModel id="model"/>
    <div class="col-12" style="margin-right: auto;
    margin-left: auto;padding: 50px 15px 20px;max-width: 850px;">
      <h4>Time series analysis</h4>
      Let's now use the time series to perform some interesting analysis:<br>
      For example, we could be interested in the temporal variation of the political vision of various politicians. This way we can find out, for example, who is the Republican politician whose ideas are becoming more polarized overtime or who is the Democrat who is taking increasingly moderate positions. To do this we fit linear regressions to the time series of the 10 most influential politicians for the two parties. The results are presented in the following interactive plot. To view a single politician just double click on his name, to hide / show a single politician just click on his name. By passing with the mouse we can see the slope expressed as score / months which indicates the change in political vision.
      <br><br>
      <div style="width: 100%;margin: 0 auto;text-align: center">
      <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/14.embed?showlink=false" height="525" width="100%"></iframe>
      </div>
      Now we can do the same analysis but for whole parties at once. That way we can understand if the general ideas of the two parties are polarizing or becoming more similar over time. In the next plot you can observe the trend of the timeseries of the average scores of the two parties and the associated linear regressions.
      <br><br>
      <div style="width: 100%;margin: 0 auto;text-align: center">
      <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/16.embed?showlink=false" height="525" width="100%"></iframe>
    </div>
    <h4>Topic specific analysis: Climate change</h4>
      It's time to observe how the distribution of scores within a party varies overtime, focusing our attention on single topics. To do this, we filter the quotations by topic, and look at the associated scores for each party. Below we show two interesting examples, but note that this analysis could be further extended (your fantasy is the limit). <br>
      More specifically, the question we are trying to answer is if, provided a set of words that portrait a given topic, we can identify the general opinions of the party and other opinions that might appear. This analysis is performed on quotes that include all of the provided words. We will show this analysis through two beautiful examples, that shows the power of this technique.
      Performing the analysis on the set of words ['climate','change'], we can observe the following:<br>
      First of all, let's plot the distribution of the political scores of both parties on the topic:<br>
      <div style="margin: 25px 0 25px 0;text-align: center">
        <img  width="60%" :src="require('@/assets/images_nicky/img_2.jpeg')" alt="distribution"/>
      </div>
      The distribution shows us, that democrats generally have the similar opinions on the subject whereas the republican party is very undecided.<br><br>

      To understand the parties's opinion on the subject, let's show some quotes that are close to the their median political score on the subject.<br>
    </div>
    <div class="col-12" style="margin-right: auto;
    margin-left: auto;padding: 0px 15px 75px;max-width: 1000px;">
        <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/44.embed?showlink=false" height="525" width="100%"></iframe>
      <br>
    </div>
    <div class="col-12" style="margin-right: auto;
    margin-left: auto;padding: 50px 15px 75px;max-width: 850px;">
      The first thing we can observe is that the median political score of the republican is not that relevant because the distribution of the party's quotes is more or less uniform. So we will have to find a better way to understand the party's opinion on the topic. However, we can see that around this value, republicans tend to say climate change is not a priority.<br>
      For democrats this analysis is more relevant because the density of quotes around the median political score is high. If we take a closer look we can see that the quotes say that climate change exists and that we must act on it. <br>

      Republicans (e.g. "... playing to his green, anti-fossil fuel , climate change crowd, ...")<br>

      Democrats (e.g "It's abundantly clear that climate change is a matter of life and death")<br>
      Now that we have seen quotes close to the median, let's look at the various opinions both parties have by showing quotes all along the political score axis. To do so, we separate our quotes into 4 intervals:<br>
      <ul style="padding-left: 50px">
        <li>interval 1: political score of quote = [0,0.25[</li>
        <li>interval 2: political score of quote = [0,0.5[</li>
        <li>interval 3: political score of quote = [0,0.75[</li>
        <li> interval 4: political score of quote = [0.75,0.1]</li>
      </ul>
      We then show a sample of the quotes that are within that interval.<br>
      <div style="width: 100%;margin: 0 auto;">
        <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/46.embed?showlink=false" height="525" width="100%"></iframe>
      </div><br>
      INTERVAL1 QUOTE: (e.g. "there are scientist that thinks a lot of different things about climat change. There are some who thinks we are warming, some thinks we are cooling, some thinks we are stable. ...")<br>
      INTERVAL2 QUOTE: (e.g. "whether it's hot or cold, the enemy is there,as far as liberals are concerned. That's why climate change is the perfect enemy ...")<br>
      INTERVAL3 QUOTE: (e.g. "We know climate change is real and it impacts our armed forces ...")<br>
      INTERVAL4 QUOTE: (e.g. "... climate change threatens the safety and security of the world ...")<br>


      We observe that the lower political score, the more politicians tend to deny the existence of climate change and say that we should do nothing about it. Whereas, the higher the political score, the more politicians tend to say that climate change exists and that acting on it is a priority. We can see that some outliers are present, reminding us that our model is far from perfect and that our per-quote accuracy is only 75% =( .<br><br>

      Finally, we plot the average political score of the politicians by averaging their quotes. This allows us to see where a politician stands on the subject and how divided a party is. The democratic party tends to all agree that climate change exists and that we should act on it. On the other hand, Republicans are divided on the subject. Some tend to agree with democrats, and others strongly disagree and deny the existence of climate change.<br>
      <div style="width: 100%;margin: 0 auto;">
        <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/48.embed?showlink=false" height="525" width="100%"></iframe>
      </div>
      <h4>Topic specific analysis: Abortion </h4>
      Let's look now at a topic where democrats and republicans are strongly divided: abortion.<br>
      By choosing the same set of words ['abortion','law'] we perform a similar analysis. Before reading the discussion you could try to take conclusion by yourself, just by looking the plots. Here are all our results:<br>
      <div style="margin: 25px 0 25px 0;text-align: center">
        <img  width="60%" :src="require('@/assets/images_nicky/img_1.jpeg')" alt="distribution"/>
      </div>
      <div style="width: 100%;margin: 0 auto;">
      <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/55.embed?showlink=false" height="525" width="100%"></iframe></div><br>
      <div style="width: 100%;margin: 0 auto;">
      <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/53.embed?showlink=false" height="525" width="100%"></iframe></div><br>

      Here we can see that the parties are strongly divided. The political scores of the republicans and democrats are polarized around 0 and 1 respectively. Around the median, which in this case represents the general opinion of the party, we see that republicans tend to be anti-abortion whereas democrats tend to be pro-abortion.<br>
      By showing the quotes along the political score axis, we see the typical scores in all the intervals:<br>
      INTERVAL1 QUOTE: (e.g. "I do want to see the number of abortion go down")<br>
      INTERVAL2 QUOTE: (e.g. "... given the potential of abortion to become a tool for genetic manipulation, the court will need to confront ...")<br>
      INTERVAL3 QUOTE: (e.g. "... it's inconsistent and will lead to a ban of abortion in Alabama, even for victims of incest or rape ...")<br>
      INTERVAL4 QUOTE: (e.g. "... it's time to pass state laws to protect womans 's constitutional right of abortion...")<br>
      Once more, the trend is the following: the lower the political score, the more a quote is anti-abortion. Whereas the higher the political score, the more a quote is pro-abortion (which should not be surprising).<br>
      Finally plotting the average political score of the politicians, we see that democrats and republicans are strongly divided. We also see that there are more quotation of rebublicans that are simultaneously containing the words abortion and law.<br>
      <iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plotly.com/~ogim/57.embed?showlink=false" height="525" width="100%"></iframe>
      
      <h4>Nice! But what comes next?</h4>
      We are very satisfied with the results achieved, being our first real experience of natural language processing. But what comes next?<br>
      First the ADA exam. Then, we can start thinking about the various improvements needed:
      <ul style="padding-left: 50px">
        <li>Including word embedding (eg: glove embedding)</li>
        <li>Try again to train a neural network</li>
        <li>Find an approach to filter non-political quotes from training data</li>
        <li>and much more...</li>
      </ul>
      If you want to give it a try it's extremely simple:
      <ul style="padding-left: 50px">
        <li>Download the pickled version of our Vectorizer and Model from google drive</li>
        <li>Load them using pickle</li>
        <li>Start making predictions</li>
        <li>and much more...</li>
      </ul>
    </div>
  </div>
</template>

<script>
//import Introduction from "./Introduction";
import Capitolo1 from "./Capitolo1";
import UseModel from "./UseModel";
export default {
  name: 'HelloWorld',
  components: {Capitolo1,UseModel},
  props: {
    msg: String
  },
  data() {
    return {
      series: [{
        name: "Desktops",
        data: [10, 41, 35, 51, 49, 62, 69, 91, 148]
      }],
      chartOptions: {
        chart: {
          height: 350,
          type: 'line',
          zoom: {
            enabled: false
          }
        },
        dataLabels: {
          enabled: false
        },
        stroke: {
          curve: 'straight'
        },
        title: {
          text: 'Product Trends by Month',
          align: 'left'
        },
        grid: {
          row: {
            colors: ['#f3f3f3', 'transparent'], // takes an array which will be repeated on columns
            opacity: 0.5
          },
        },
        xaxis: {
          categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep'],
        }
      },
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hsg{
  max-width: 1080px;
  margin: 0 auto;
  padding: 0 1rem;
  box-sizing: content-box;
}
li{
  margin: 10px 0;
}
h4{
  font-size: 1.875rem;
}
.idgraph {
  -webkit-transform: scale(0.75);
}
</style>
