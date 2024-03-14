# webscrapping from websites 
import requests
from bs4 import BeautifulSoup
from textblob import TextBlob
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize, sent_tokenize
import re
import syllables
from collections import Counter
import pandas as pd

# Function to perform sentiment analysis, text cleaning, and extract derived variables from a given URL
def analyze_text(url, url_id):
    # Fetch web page content
    response = requests.get(url)
    soup = BeautifulSoup(response.text, "html.parser")

    # Extract text content from the webpage
    text_content = soup.get_text()

    # Clean text by removing non-alphabetic characters, converting to lowercase, and removing stopwords
    cleaned_text = ' '.join([word.lower() for word in re.findall(r'\b\w+\b', text_content) if word.lower() not in stopwords.words('english')])

    # Perform sentiment analysis using TextBlob
    blob = TextBlob(cleaned_text)
    sentiment_score = blob.sentiment.polarity
    subjectivity_score = blob.sentiment.subjectivity

    # Extract derived variables
    sentences = sent_tokenize(text_content)
    words = word_tokenize(text_content)

    # Additional Metrics
    positive_words = ["good", "happy", "positive","great","boon","growth","creative","better","beneficial"]  # Add more positive words
    negative_words = ["bad", "impact", "negative","bane","harm","harmful","not","no","nothing","none"]     # Add more negative words

    positive_score = sum(1 for word in words if word.lower() in positive_words)
    negative_score = sum(1 for word in words if word.lower() in negative_words)

    polarity_score = sentiment_score
    avg_sentence_length = len(words) / len(sentences)
    percentage_complex_words = sum(1 for word in words if syllables.estimate(word) >= 3) / len(words) * 100
    fog_index = 0.4 * (avg_sentence_length + percentage_complex_words)

    complex_word_count = sum(1 for word in words if syllables.estimate(word) >= 3)
    word_count = len(words)
    syllable_per_word = sum(syllables.estimate(word) for word in words) / len(words)

    personal_pronouns = sum(1 for word in words if word.lower() in ["i", "me", "my", "mine", "myself"])

    avg_word_length = sum(len(word) for word in words) / len(words)
    avg_words_per_sentence = len(words) / len(sentences)

    # Display or return the results as needed
    results = {
        "URL ID": url_id,
        "URL": url,
        "Sentiment Score": sentiment_score,
        "Subjectivity Score": subjectivity_score,
        "Positive Score": positive_score,
        "Negative Score": negative_score,
        "Polarity Score": polarity_score,
        "Avg Sentence Length": avg_sentence_length,
        "Percentage of Complex Words": percentage_complex_words,
        "Fog Index": fog_index,
        "Avg Words Per Sentence": avg_words_per_sentence,
        "Complex Word Count": complex_word_count,
        "Word Count": word_count,
        "Syllable Per Word": syllable_per_word,
        "Personal Pronouns": personal_pronouns,
        "Avg Word Length": avg_word_length
    }

    return results

# List of URLs with associated IDs
url_id_mapping = {
"blackassign0001" :"https://insights.blackcoffer.com/rising-it-cities-and-its-impact-on-the-economy-environment-infrastructure-and-city-life-by-the-year-2040-2/",
"blackassign0002"	:"https://insights.blackcoffer.com/rising-it-cities-and-their-impact-on-the-economy-environment-infrastructure-and-city-life-in-future/",
"blackassign0003"	:"https://insights.blackcoffer.com/internet-demands-evolution-communication-impact-and-2035s-alternative-pathways/",
"blackassign0004"	:"https://insights.blackcoffer.com/rise-of-cybercrime-and-its-effect-in-upcoming-future/",
"blackassign0005"	:"https://insights.blackcoffer.com/ott-platform-and-its-impact-on-the-entertainment-industry-in-future/",
"blackassign0006"	:"https://insights.blackcoffer.com/the-rise-of-the-ott-platform-and-its-impact-on-the-entertainment-industry-by-2040/",
"blackassign0007"	:"https://insights.blackcoffer.com/rise-of-cyber-crime-and-its-effects/",
"blackassign0008"	:"https://insights.blackcoffer.com/rise-of-internet-demand-and-its-impact-on-communications-and-alternatives-by-the-year-2035-2/",
"blackassign0009"	:"https://insights.blackcoffer.com/rise-of-cybercrime-and-its-effect-by-the-year-2040-2/",
"blackassign0010"	:"https://insights.blackcoffer.com/rise-of-cybercrime-and-its-effect-by-the-year-2040/",
"blackassign0011"	:"https://insights.blackcoffer.com/rise-of-internet-demand-and-its-impact-on-communications-and-alternatives-by-the-year-2035/",
"blackassign0012"	:"https://insights.blackcoffer.com/rise-of-telemedicine-and-its-impact-on-livelihood-by-2040-3-2/",
"blackassign0013"	:"https://insights.blackcoffer.com/rise-of-e-health-and-its-impact-on-humans-by-the-year-2030/",
"blackassign0014"	:"https://insights.blackcoffer.com/rise-of-e-health-and-its-imapct-on-humans-by-the-year-2030-2/",
"blackassign0015"	:"https://insights.blackcoffer.com/rise-of-telemedicine-and-its-impact-on-livelihood-by-2040-2/",
"blackassign0016"	:"https://insights.blackcoffer.com/rise-of-telemedicine-and-its-impact-on-livelihood-by-2040-2-2/",
"blackassign0017"	:"https://insights.blackcoffer.com/rise-of-chatbots-and-its-impact-on-customer-support-by-the-year-2040/",
"blackassign0018"	:"https://insights.blackcoffer.com/rise-of-e-health-and-its-imapct-on-humans-by-the-year-2030/",
"blackassign0019"	:"https://insights.blackcoffer.com/how-does-marketing-influence-businesses-and-consumers/",
"blackassign0020"	:"https://insights.blackcoffer.com/how-advertisement-increase-your-market-value/",
"blackassign0021"	:"https://insights.blackcoffer.com/negative-effects-of-marketing-on-society/",
"blackassign0022"	:"https://insights.blackcoffer.com/how-advertisement-marketing-affects-business/",
"blackassign0023"	:"https://insights.blackcoffer.com/rising-it-cities-will-impact-the-economy-environment-infrastructure-and-city-life-by-the-year-2035/",
"blackassign0024"	:"https://insights.blackcoffer.com/rise-of-ott-platform-and-its-impact-on-entertainment-industry-by-the-year-2030/",
"blackassign0025"	:"https://insights.blackcoffer.com/rise-of-electric-vehicles-and-its-impact-on-livelihood-by-2040/",
"blackassign0026"	:"https://insights.blackcoffer.com/rise-of-electric-vehicle-and-its-impact-on-livelihood-by-the-year-2040/",
"blackassign0027"	:"https://insights.blackcoffer.com/oil-prices-by-the-year-2040-and-how-it-will-impact-the-world-economy/",
"blackassign0028"	:"https://insights.blackcoffer.com/an-outlook-of-healthcare-by-the-year-2040-and-how-it-will-impact-human-lives/",
"blackassign0029"	:"https://insights.blackcoffer.com/ai-in-healthcare-to-improve-patient-outcomes/",
"blackassign0030"	:"https://insights.blackcoffer.com/what-if-the-creation-is-taking-over-the-creator/",
"blackassign0031"	:"https://insights.blackcoffer.com/what-jobs-will-robots-take-from-humans-in-the-future/",
"blackassign0032"	:"https://insights.blackcoffer.com/will-machine-replace-the-human-in-the-future-of-work/",
"blackassign0033"	:"https://insights.blackcoffer.com/will-ai-replace-us-or-work-with-us/",
"blackassign0034"	:"https://insights.blackcoffer.com/man-and-machines-together-machines-are-more-diligent-than-humans-blackcoffe/",
"blackassign0035"	:"https://insights.blackcoffer.com/in-future-or-in-upcoming-years-humans-and-machines-are-going-to-work-together-in-every-field-of-work/",
"blackassign0036"	:"https://insights.blackcoffer.com/how-neural-networks-can-be-applied-in-various-areas-in-the-future/",
"blackassign0037"	:"https://insights.blackcoffer.com/how-machine-learning-will-affect-your-business/",
"blackassign0038"	:"https://insights.blackcoffer.com/deep-learning-impact-on-areas-of-e-learning/",
"blackassign0039"	:"https://insights.blackcoffer.com/how-to-protect-future-data-and-its-privacy-blackcoffer/",
"blackassign0040"	:"https://insights.blackcoffer.com/how-machines-ai-automations-and-robo-human-are-effective-in-finance-and-banking/",
"blackassign0041"	:"https://insights.blackcoffer.com/ai-human-robotics-machine-future-planet-blackcoffer-thinking-jobs-workplace/",
"blackassign0042"	:"https://insights.blackcoffer.com/how-ai-will-change-the-world-blackcoffer/",
"blackassign0043"	:"https://insights.blackcoffer.com/future-of-work-how-ai-has-entered-the-workplace/",
"blackassign0044"	:"https://insights.blackcoffer.com/ai-tool-alexa-google-assistant-finance-banking-tool-future/",
"blackassign0045"	:"https://insights.blackcoffer.com/ai-healthcare-revolution-ml-technology-algorithm-google-analytics-industrialrevolution/",
"blackassign0046"	:"https://insights.blackcoffer.com/all-you-need-to-know-about-online-marketing/",
"blackassign0047"	:"https://insights.blackcoffer.com/evolution-of-advertising-industry/",
"blackassign0048"	:"https://insights.blackcoffer.com/how-data-analytics-can-help-your-business-respond-to-the-impact-of-covid-19/",
"blackassign0049"	:"https://insights.blackcoffer.com/covid-19-environmental-impact-for-the-future/",
"blackassign0050"	:"https://insights.blackcoffer.com/environmental-impact-of-the-covid-19-pandemic-lesson-for-the-future/",
"blackassign0051"	:"https://insights.blackcoffer.com/how-data-analytics-and-ai-are-used-to-halt-the-covid-19-pandemic/",
"blackassign0052"	:"https://insights.blackcoffer.com/difference-between-artificial-intelligence-machine-learning-statistics-and-data-mining/",
"blackassign0053"	:"https://insights.blackcoffer.com/how-python-became-the-first-choice-for-data-science/",
"blackassign0054"	:"https://insights.blackcoffer.com/how-google-fit-measure-heart-and-respiratory-rates-using-a-phone/",
"blackassign0055"	:"https://insights.blackcoffer.com/what-is-the-future-of-mobile-apps/",
"blackassign0056"	:"https://insights.blackcoffer.com/impact-of-ai-in-health-and-medicine/",
"blackassign0057"	:"https://insights.blackcoffer.com/telemedicine-what-patients-like-and-dislike-about-it/",
"blackassign0058"	:"https://insights.blackcoffer.com/how-we-forecast-future-technologies/",
"blackassign0059"	:"https://insights.blackcoffer.com/can-robots-tackle-late-life-loneliness/",
"blackassign0060"	:"https://insights.blackcoffer.com/embedding-care-robots-into-society-socio-technical-considerations/",
"blackassign0061"	:"https://insights.blackcoffer.com/management-challenges-for-future-digitalization-of-healthcare-services/",
"blackassign0062"	:"https://insights.blackcoffer.com/are-we-any-closer-to-preventing-a-nuclear-holocaust/",
"blackassign0063"	:"https://insights.blackcoffer.com/will-technology-eliminate-the-need-for-animal-testing-in-drug-development/",
"blackassign0064"	:"https://insights.blackcoffer.com/will-we-ever-understand-the-nature-of-consciousness/",
"blackassign0065"	:"https://insights.blackcoffer.com/will-we-ever-colonize-outer-space/",
"blackassign0066"	:"https://insights.blackcoffer.com/what-is-the-chance-homo-sapiens-will-survive-for-the-next-500-years/",
"blackassign0067"	:"https://insights.blackcoffer.com/why-does-your-business-need-a-chatbot/",
"blackassign0068"	:"https://insights.blackcoffer.com/how-you-lead-a-project-or-a-team-without-any-technical-expertise/",
"blackassign0069"	:"https://insights.blackcoffer.com/can-you-be-great-leader-without-technical-expertise/",
"blackassign0070"	:"https://insights.blackcoffer.com/how-does-artificial-intelligence-affect-the-environment/",
"blackassign0071"	:"https://insights.blackcoffer.com/how-to-overcome-your-fear-of-making-mistakes-2/",
"blackassign0072"	:"https://insights.blackcoffer.com/is-perfection-the-greatest-enemy-of-productivity/",
"blackassign0073"	:"https://insights.blackcoffer.com/global-financial-crisis-2008-causes-effects-and-its-solution/",
"blackassign0074"	:"https://insights.blackcoffer.com/gender-diversity-and-equality-in-the-tech-industry/",
"blackassign0075"	:"https://insights.blackcoffer.com/how-to-overcome-your-fear-of-making-mistakes/",
"blackassign0076"	:"https://insights.blackcoffer.com/how-small-business-can-survive-the-coronavirus-crisis/",
"blackassign0077"	:"https://insights.blackcoffer.com/impacts-of-covid-19-on-vegetable-vendors-and-food-stalls/",
"blackassign0078"	:"https://insights.blackcoffer.com/impacts-of-covid-19-on-vegetable-vendors/",
"blackassign0079"	:"https://insights.blackcoffer.com/impact-of-covid-19-pandemic-on-tourism-aviation-industries/",
"blackassign0080"	:"https://insights.blackcoffer.com/impact-of-covid-19-pandemic-on-sports-events-around-the-world/",
"blackassign0081"	:"https://insights.blackcoffer.com/changing-landscape-and-emerging-trends-in-the-indian-it-ites-industry/",
"blackassign0082"	:"https://insights.blackcoffer.com/online-gaming-adolescent-online-gaming-effects-demotivated-depression-musculoskeletal-and-psychosomatic-symptoms/",
"blackassign0083"	:"https://insights.blackcoffer.com/human-rights-outlook/",
"blackassign0084"	:"https://insights.blackcoffer.com/how-voice-search-makes-your-business-a-successful-business/",
"blackassign0085"	:"https://insights.blackcoffer.com/how-the-covid-19-crisis-is-redefining-jobs-and-services/",
"blackassign0086"	:"https://insights.blackcoffer.com/how-to-increase-social-media-engagement-for-marketers/",
"blackassign0087"	:"https://insights.blackcoffer.com/impacts-of-covid-19-on-streets-sides-food-stalls/",
"blackassign0088"	:"https://insights.blackcoffer.com/coronavirus-impact-on-energy-markets-2/",
"blackassign0089"	:"https://insights.blackcoffer.com/coronavirus-impact-on-the-hospitality-industry-5/",
"blackassign0090"	:"https://insights.blackcoffer.com/lessons-from-the-past-some-key-learnings-relevant-to-the-coronavirus-crisis-4/",
"blackassign0091"	:"https://insights.blackcoffer.com/estimating-the-impact-of-covid-19-on-the-world-of-work-2/",
"blackassign0092"	:"https://insights.blackcoffer.com/estimating-the-impact-of-covid-19-on-the-world-of-work-3/",
"blackassign0093"	:"https://insights.blackcoffer.com/travel-and-tourism-outlook/",
"blackassign0094"	:"https://insights.blackcoffer.com/gaming-disorder-and-effects-of-gaming-on-health/",
"blackassign0095"	:"https://insights.blackcoffer.com/what-is-the-repercussion-of-the-environment-due-to-the-covid-19-pandemic-situation/",
"blackassign0096"	:"https://insights.blackcoffer.com/what-is-the-repercussion-of-the-environment-due-to-the-covid-19-pandemic-situation-2/",
"blackassign0097"	:"https://insights.blackcoffer.com/impact-of-covid-19-pandemic-on-office-space-and-co-working-industries/",
"blackassign0098"	:"https://insights.blackcoffer.com/contribution-of-handicrafts-visual-arts-literature-in-the-indian-economy/",
"blackassign0099"	:"https://insights.blackcoffer.com/how-covid-19-is-impacting-payment-preferences/",
"blackassign0100"	:"https://insights.blackcoffer.com/how-will-covid-19-affect-the-world-of-work-2/"


}
# Initialize an empty list to store analysis results
analysis_results_list = []

# Analyze each URL and store the results in a list
for url_id, url in url_id_mapping.items():
    analysis_results = analyze_text(url, url_id)
    analysis_results_list.append(analysis_results)

# Convert the list of results to a DataFrame
df = pd.DataFrame(analysis_results_list)

# Export the DataFrame to Excel
excel_filename = "analysis_results.xlsx"
df.to_excel(excel_filename, index=False)
print(f"Results exported to {excel_filename}")

