# Chatbot

## APIs
In this assignment, I have incorporated two extra APIs: Flickr API and Google Translate API. 

### Flickr API
The Flickr API searches what the user is asking in certain situations and returns a related image. First applying for a key on its website is required, after getting the key, you could go generate a link that is used for fetching images in JSON format. The link could be copied into the file where it is used, then the searching content is specified from reading user's input. After getting results in JSON format, it could be read into a python object with its attributes turning into the object's. After this, another website that is specified by Flickr is used to retrieve the actual image using JSON and its attributes. Details will be shown in the method.

A function named flickr_search(str) is defined in class GUI, and when calling it from outside the class, an instance of GUI class named "app" is created, then app.flickr_search(str) is called. Here is the code:

```
    def flickr_search(self,tag):
        tag = tag.strip()
        tag = tag.replace(" ","+")
        ur = 'https://www.flickr.com/services/rest/?method=flickr.photos.search&api_key=6a87e58a6a39d92c88487c747867f394&text='+tag +'&format=json&nojsoncallback=1'
        fil = urllib.request.urlopen(ur)
        fil = fil.read()
        #def _json_object_hook(d): return namedtuple('X', d.keys())(*d.values())
        dat = json.loads(fil,object_hook=lambda d: SimpleNamespace(**d))
    
        photos = dat.photos.photo
        count = 0;
        im = None
        for photo in photos:
            if(count>0):
                break
            count+=1
            result = 'http://live.staticflickr.com/' + str(photo.server) + '/' + str(photo.id) + '_' + photo.secret + '.jpg'
            fd = urllib.request.urlopen(result)
            image_file = io.BytesIO(fd.read())
            tmpimg = Image.open(image_file)
            tmpimg = tmpimg.resize((250,150), Image. ANTIALIAS)
            im = ImageTk.PhotoImage(tmpimg)
            global search_img
            search_img = im
            ChatLog.image_create(END, image = search_img)
            ChatLog.insert(END, "\n\n")
        return im 
```

### Google Translate API
The Google Translate API could translate the reply of the bot into different languages. In this project, it is specified to Chinese. Basic steps for setting up the environment for Google Translate API is the same as Flickr API. First you need to apply for a key, then you need to download the key in a JSON file and store it. Then set up in python like this:

```
path="C:/Users/sword/Documents/GitHub/Chat-bot-team-20/code/my-key.json"
os.environ['GOOGLE_APPLICATION_CREDENTIALS'] = path
```
where the path references the path where the key is stored, then add it to the system environment. Also a function is defined in the website. It is incorporated with some minor modifications:
```
def translate_text(target, text):
    """Translates text into the target language.

    Target must be an ISO 639-1 language code.
    See https://g.co/cloud/translate/v2/translate-reference#supported_languages
    """
    import six
    from google.cloud import translate_v2 as translate

    translate_client = translate.Client()

    if isinstance(text, six.binary_type):
        text = text.decode("utf-8")

    # Text can also be a sequence of strings, in which case this method
    # will return a sequence of results for each text.
    result = translate_client.translate(text, target_language=target)
    
    return result["translatedText"]
```
When calling the method, the first parameter specifies the target language, which is Chinese here, denoted by "zh". Then the second parameter is the text you want to translate. And the function returns the text in Chinese in string.
