<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "e5ea5e7582f70008ea9bec3b3820f20a",
  "translation_date": "2025-05-17T14:23:38+00:00",
  "source_file": "04-PracticalImplementation/samples/java/containerapp/README.md",
  "language_code": "hi"
}
-->
## सिस्टम आर्किटेक्चर

यह प्रोजेक्ट एक वेब एप्लिकेशन का प्रदर्शन करता है जो उपयोगकर्ता संकेतों को एक कैलकुलेटर सेवा के पास भेजने से पहले सामग्री सुरक्षा की जांच करता है, जो मॉडल कॉन्टेक्स्ट प्रोटोकॉल (MCP) के माध्यम से होती है।

### यह कैसे काम करता है

1. **उपयोगकर्ता इनपुट**: उपयोगकर्ता वेब इंटरफेस में एक गणना संकेत दर्ज करता है
2. **सामग्री सुरक्षा स्क्रीनिंग (इनपुट)**: संकेत का Azure Content Safety API द्वारा विश्लेषण किया जाता है
3. **सुरक्षा निर्णय (इनपुट)**:
   - यदि सामग्री सुरक्षित है (सभी श्रेणियों में गंभीरता < 2), तो यह कैलकुलेटर के पास जाती है
   - यदि सामग्री संभावित रूप से हानिकारक है, तो प्रक्रिया रुक जाती है और चेतावनी लौटाई जाती है
4. **कैलकुलेटर इंटीग्रेशन**: सुरक्षित सामग्री को LangChain4j द्वारा संसाधित किया जाता है, जो MCP कैलकुलेटर सर्वर के साथ संवाद करता है
5. **सामग्री सुरक्षा स्क्रीनिंग (आउटपुट)**: बॉट के उत्तर का Azure Content Safety API द्वारा विश्लेषण किया जाता है
6. **सुरक्षा निर्णय (आउटपुट)**:
   - यदि बॉट का उत्तर सुरक्षित है, तो यह उपयोगकर्ता को दिखाया जाता है
   - यदि बॉट का उत्तर संभावित रूप से हानिकारक है, तो इसे चेतावनी से बदल दिया जाता है
7. **उत्तर**: परिणाम (यदि सुरक्षित) उपयोगकर्ता को दोनों सुरक्षा विश्लेषणों के साथ दिखाए जाते हैं

## मॉडल कॉन्टेक्स्ट प्रोटोकॉल (MCP) के साथ कैलकुलेटर सेवाओं का उपयोग

यह प्रोजेक्ट दिखाता है कि LangChain4j से कैलकुलेटर MCP सेवाओं को कॉल करने के लिए मॉडल कॉन्टेक्स्ट प्रोटोकॉल (MCP) का उपयोग कैसे किया जाए। यह कार्यान्वयन स्थानीय MCP सर्वर का उपयोग करता है जो पोर्ट 8080 पर चल रहा है ताकि कैलकुलेटर संचालन प्रदान किया जा सके।

### Azure Content Safety सेवा सेट अप करना

सामग्री सुरक्षा सुविधाओं का उपयोग करने से पहले, आपको Azure Content Safety सेवा संसाधन बनाना होगा:

1. [Azure Portal](https://portal.azure.com) में साइन इन करें
2. "Create a resource" पर क्लिक करें और "Content Safety" खोजें
3. "Content Safety" चुनें और "Create" पर क्लिक करें
4. अपने संसाधन के लिए एक अनूठा नाम दर्ज करें
5. अपनी सदस्यता और संसाधन समूह चुनें (या एक नया बनाएं)
6. एक समर्थित क्षेत्र चुनें (विवरण के लिए [Region availability](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=cognitive-services) देखें)
7. एक उपयुक्त मूल्य निर्धारण स्तर चुनें
8. संसाधन को तैनात करने के लिए "Create" पर क्लिक करें
9. एक बार तैनाती पूरी होने के बाद, "Go to resource" पर क्लिक करें
10. बाएँ पैन में, "Resource Management" के तहत "Keys and Endpoint" चुनें
11. अगले चरण में उपयोग के लिए किसी भी कुंजी और एंडपॉइंट URL को कॉपी करें

### पर्यावरण चर कॉन्फ़िगर करना

GitHub मॉडल प्रमाणीकरण के लिए `GITHUB_TOKEN` पर्यावरण चर सेट करें:
```sh
export GITHUB_TOKEN=<your_github_token>
```

सामग्री सुरक्षा सुविधाओं के लिए, सेट करें:
```sh
export CONTENT_SAFETY_ENDPOINT=<your_content_safety_endpoint>
export CONTENT_SAFETY_KEY=<your_content_safety_key>
```

ये पर्यावरण चर एप्लिकेशन द्वारा Azure Content Safety सेवा के साथ प्रमाणीकरण करने के लिए उपयोग किए जाते हैं। यदि ये चर सेट नहीं हैं, तो एप्लिकेशन प्रदर्शन उद्देश्यों के लिए प्लेसहोल्डर मानों का उपयोग करेगा, लेकिन सामग्री सुरक्षा सुविधाएँ ठीक से काम नहीं करेंगी।

### कैलकुलेटर MCP सर्वर शुरू करना

क्लाइंट चलाने से पहले, आपको localhost:8080 पर SSE मोड में कैलकुलेटर MCP सर्वर शुरू करना होगा।

## प्रोजेक्ट विवरण

यह प्रोजेक्ट LangChain4j के साथ Model Context Protocol (MCP) के एकीकरण का प्रदर्शन करता है ताकि कैलकुलेटर सेवाओं को कॉल किया जा सके। प्रमुख विशेषताओं में शामिल हैं:

- मूल गणितीय संचालन के लिए एक कैलकुलेटर सेवा से कनेक्ट करने के लिए MCP का उपयोग करना
- उपयोगकर्ता संकेतों और बॉट प्रतिक्रियाओं पर दोहरी-स्तर सामग्री सुरक्षा जाँच
- LangChain4j के माध्यम से GitHub के gpt-4.1-nano मॉडल के साथ एकीकरण
- MCP परिवहन के लिए सर्वर-सेंट इवेंट्स (SSE) का उपयोग करना

## सामग्री सुरक्षा एकीकरण

प्रोजेक्ट में व्यापक सामग्री सुरक्षा सुविधाएँ शामिल हैं ताकि यह सुनिश्चित किया जा सके कि उपयोगकर्ता इनपुट और सिस्टम प्रतिक्रियाएँ हानिकारक सामग्री से मुक्त हों:

1. **इनपुट स्क्रीनिंग**: सभी उपयोगकर्ता संकेतों को हानिकारक सामग्री श्रेणियों जैसे कि घृणास्पद भाषण, हिंसा, आत्म-हानि और यौन सामग्री के लिए प्रसंस्करण से पहले विश्लेषण किया जाता है।

2. **आउटपुट स्क्रीनिंग**: संभावित रूप से अनसेंसर्ड मॉडल का उपयोग करते समय भी, सिस्टम सभी उत्पन्न प्रतिक्रियाओं को समान सामग्री सुरक्षा फ़िल्टर के माध्यम से उपयोगकर्ता को दिखाने से पहले जाँचता है।

यह दोहरी-स्तरीय दृष्टिकोण सुनिश्चित करता है कि सिस्टम सुरक्षित रहता है, चाहे जो भी AI मॉडल उपयोग किया जा रहा हो, उपयोगकर्ताओं को हानिकारक इनपुट और संभावित समस्याग्रस्त AI-जनित आउटपुट दोनों से बचाता है।

## वेब क्लाइंट

एप्लिकेशन में एक उपयोगकर्ता-अनुकूल वेब इंटरफेस शामिल है जो उपयोगकर्ताओं को सामग्री सुरक्षा कैलकुलेटर सिस्टम के साथ बातचीत करने की अनुमति देता है:

### वेब इंटरफेस सुविधाएँ

- गणना संकेत दर्ज करने के लिए सरल, सहज रूप
- दोहरी-स्तरीय सामग्री सुरक्षा मान्यता (इनपुट और आउटपुट)
- संकेत और प्रतिक्रिया सुरक्षा पर रीयल-टाइम फीडबैक
- आसान व्याख्या के लिए रंग-कोडित सुरक्षा संकेतक
- विभिन्न उपकरणों पर काम करने वाला साफ, उत्तरदायी डिज़ाइन
- उपयोगकर्ताओं को मार्गदर्शन करने के लिए सुरक्षित संकेतों के उदाहरण

### वेब क्लाइंट का उपयोग करना

1. एप्लिकेशन शुरू करें:
   ```sh
   mvn spring-boot:run
   ```

2. अपने ब्राउज़र को खोलें और `http://localhost:8087` पर जाएं

3. प्रदान किए गए टेक्स्ट एरिया में एक गणना संकेत दर्ज करें (जैसे, "24.5 और 17.3 का योग निकालें")

4. अपनी अनुरोध प्रक्रिया के लिए "Submit" पर क्लिक करें

5. परिणाम देखें, जिसमें शामिल होंगे:
   - आपके संकेत का सामग्री सुरक्षा विश्लेषण
   - गणना किया गया परिणाम (यदि संकेत सुरक्षित था)
   - बॉट की प्रतिक्रिया का सामग्री सुरक्षा विश्लेषण
   - कोई सुरक्षा चेतावनी यदि इनपुट या आउटपुट में से कोई भी चिह्नित किया गया था

वेब क्लाइंट स्वचालित रूप से दोनों सामग्री सुरक्षा सत्यापन प्रक्रियाओं को संभालता है, यह सुनिश्चित करते हुए कि सभी इंटरैक्शन सुरक्षित और उपयुक्त हैं, चाहे जो भी AI मॉडल उपयोग किया जा रहा हो।

**अस्वीकरण**:  
यह दस्तावेज़ AI अनुवाद सेवा [Co-op Translator](https://github.com/Azure/co-op-translator) का उपयोग करके अनुवादित किया गया है। जबकि हम सटीकता के लिए प्रयास करते हैं, कृपया ध्यान दें कि स्वचालित अनुवादों में त्रुटियाँ या अशुद्धियाँ हो सकती हैं। मूल दस्तावेज़ को इसकी मूल भाषा में आधिकारिक स्रोत माना जाना चाहिए। महत्वपूर्ण जानकारी के लिए, पेशेवर मानव अनुवाद की सिफारिश की जाती है। इस अनुवाद के उपयोग से उत्पन्न किसी भी गलतफहमी या गलत व्याख्या के लिए हम उत्तरदायी नहीं हैं।