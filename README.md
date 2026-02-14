# Voice-Assistant-with-OpenAI-s-GPT-3-and-IBM-Watson
Create a voice assistant using OpenAI and IBM Watson Speech Libraries for Embed


## Step 1: Understanding the interface
  To create an interface that allows communication with a voice assistant, and a backend to manage the sending and receiving of responses.
    Run the following commands to receive the outline of the project, rename it to save it with another name and finally move into that directory.
      git clone https://github.com/ibm-developer-skills-network/bkrva-chatapp-with-voice-and-openai-outline.git
      mv bkrva-chatapp-with-voice-and-openai-outline chatapp-with-voice-and-openai-outline
      cd chatapp-with-voice-and-openai-outline.

     Brief understanding of how the frontend works:
      HTML, CSS, and Javascript
      The index.html file is responsible for the layout and structure of the web interface. This file contains the code for incorporating external libraries such as JQuery, Bootstrap, and FontAwesome Icons, as well as the CSS (style.css) and Javascript code (script.js) that control the styling and interactivity of the interface.
      
      The style.css file is responsible for customizing the visual appearance of the page's components. It also handles the loading animation using CSS keyframes. Keyframes are a way of defining the values of an animation at various points in time, allowing for a smooth transition between different styles and creating dynamic animations.
      
      The script.js file is responsible for the page's interactivity and functionality. It contains the majority of the code and handles all the necessary functions such as switching between light and dark mode, sending messages, and displaying new messages on the screen. It even enables the users to record audio.

## Step 2: Understanding the server
  When a user interacts with the voice assistant through the frontend interface, the request will be sent to the Flask backend. Flask will then process the request and send it to the appropriate service.
  The code provided gives the outline for the server in the server.py file.
  At the top of the file, there are several import statements. These statements are used to bring in external libraries and modules, which will be used in the current file. For instance, speech_to_text is a function inside the worker.py file, while openai is a package that needs to be installed to use the OpenAI's GPT-3 model. These imported packages, modules and libraries will allow you to access the additional functionalities and methods that they offer, making it easy to interact with the speech-to-text and GPT-3 model in your code.

  Underneath the imports, the Flask application is initialized, and a CORS policy is set. A CORS policy is used to allow or prevent web pages from making requests to different domains than the one that served the web page.

## Step 3: Running the application
  The git clone from Step 1 already comes with a Dockerfile and requirements.txt for this application. These files are used to build the image with the dependencies already installed. Looking into the Dockerfile you can see it’s fairly simple, it just creates a python environment, moves all the files from the local directory to the container, installs the required packages, and then starts the application by running the python command.
    Small prerequisites:
      mkdir /home/project/chatapp-with-voice-and-openai-outline/certs/
      cp /usr/local/share/ca-certificates/rootCA.crt /home/project/chatapp-with-voice-and-openai-outline/certs/

  ### 1. Starting the application
        docker build . -t voice-chatapp-powered-by-openai
        docker run -p 8000:8000 voice-chatapp-powered-by-openai
  ### 2. Starting Speech-to-Text
        Skills Network provides its own Watson Speech-to-Text image that runs automatically in this environment. 
        base_url = "https://sn-watson-stt.labs.skills.network"
  ### 3. Starting Text-to-Speech
        Skills Network provides its own Watson Text-to-Speech image that is run automatically in this environment. 
        base_url = "https://sn-watson-tts.labs.skills.network"

## Step 4: Integrating Watson Speech-to-Text
        The speech_to_text function will take in audio data as a parameter, make an API call to the Watson Speech-to-Text API using the requests library, and return the transcription of the audio data.
        The function simply takes audio_binary as the only parameter and then sends it in the body of the HTTP request.

        To make an HTTP Post request to Watson Speech-to-Text API, you need the following:
        
        URL of the API: This is defined as api_url in your code and points to the Watson's Speech-to-Text service
        Parameters: This is defined as params in your code. It's just a dictionary having one key-value pair i.e. 'model': 'en-US_Multimedia' which simply tells Watson that you want to use the US English model for processing your speech
        Body of the request: this is defined as body and is equal to audio_binary since you are sending the audio data inside the body of your POST request.
        You then use the requests library to send this HTTP request passing in the url, params, and data(body) to it and then use .json() to convert the API's response to json format which is very easy to parse and can be treated like a dictionary in Python.
## Step 5: Integrating OpenAI API
        openai_process_message, which will take in a prompt and pass it to OpenAI's GPT-3 API to receive a response. Essentially, it's the equivalent of pressing the send button to get a response from ChatGPT.
        The function is really simple, thanks to the very easy to use openai library.

        This is where you can give your personal assistant some personality. In this case you are telling the model to become a personal assistant by: Act like a personal assistant, and then giving it’s specific tasks its capable of doing: You can respond to questions, translate sentences, summarize news, and give recommendations.. By adding the original user message afterwards, it gives OpenAI more room to sound genuine. Feel free to change this according to what you require.
        
        Then you call OpenAI's API by using openai.chat.completions.create function and pass in the following 3 parameters:
        
        model: This is the OpenAI model we want to use for processing our prompt, in this case we are using their gpt-5-nano model.
        messages: The messages parameter is an array of objects used to define the conversation flow between the user and the AI. Each object represents a message with two key attributes: role (identifying the sender as either “system” for setup instructions or “user” for the actual user query) and content (the message text). The “system” role message instructs the AI on how to behave (for example, acting like a personal assistant), while the “user” role message contains the user's input. This structured approach helps tailor the AI's responses to be more relevant and personalized.
        max_completion_tokens: This defines the maximum number of tokens the model can produce in its reply. With a setting of 1000, the response can be fairly detailed.
## Step 6: Integrating Watson Text-to-Speech
        In the worker.py file, the text_to_speech function passes data to Watson's Text-to-Speech API to get the data as spoken output.

        This function is going to be very similar to speech_to_text as you will be utilizing your request library again to make an HTTP request. Let's dive into the code.
        The function simply takes text and voice as the parameters. It adds voice as a parameter to the api_url if it's not empty or not default. It sends the text in the body of the HTTP request.

        Similarly, as before, to make an HTTP Post request to Watson Text-to-Speech API, you need the following three elements:
        
        URL of the API: This is defined as api_url in your code and points to Watson's Text to Speech service. This time you also append a voice parameter to the api_url if the user has sent a preferred voice in their request.
        Headers: This is defined as headers in your code. It's just a dictionary having two key-value pairs. The first is 'Accept':'audio/wav' which tells Watson that we are sending an audio having wav format. The second one is ‘Content-Type’:’application/json’, which means that the format of the body would be JSON
        Body of the request: This is defined as json_data and is a dictionary containing ‘text’:text key-value pair, this text will then be processed and converted to a speech.
        We then use the requests library to send this HTTP request passing in the URL, headers, and json(body) to it and then use .json() to convert the API's response to json format so we can parse it.
## Step 7: Putting everything together by creating Flask API endpoints
        Speech-to-Text route
          This function is very simple, as it only converts the user's speech-to-text using thespeech_to_text we defined in one of our previous sections and return the response.
          We will start by storing user's message in user_message by using request.json['userMessage']. Similarly, we will also store the user's preferred voice in voice by using request.json['voice'].

          We will then use the helper function we defined earlier to process this user's message by calling openai_process_message(user_message) and storing the response in openai_response_text. We will then clean this response to remove any empty lines by using a simple one liner function in python, os.linesep.join([s for s in openai_response_text.splitlines() if s]).
          
          Once we have this response cleaned, we will now use another helper function we defined earlier to convert it to speech. Therefore, we will call text_to_speech and pass in the two required parameters which are openai_response_text and voice. We will store the function's return value in a variable called openai_response_speech.
          
          As the openai_response_speech is a type of audio data, we can't directly send this inside a json as it can only store textual data. Therefore, we will be using something called “base64 encoding”. In simple words, we can convert any type of binary data to a textual representation by encoding the data in base64 format. Hence, we will simply use base64.b64encode(openai_response_speech).decode('utf-8') and store the result back to openai_response_speech.
          
          Now we have everything ready for our response so finally we will be using the same app.response_class function and send in the three parameters required. The status and mimetype will be exactly the same as we defined them in our previous speech_to_text_route. In the response, we will use json.dumps function as we did before and will pass in a dictionary as a parameter containing "openaiResponseText":openai_response_text and "openaiResponseSpeech":openai_response_speech.
          
          We then return the response.



  ## Step 8: Testing your personal assistant
      docker build . -t voice-chatapp-powered-by-openai
      docker run -p 8000:8000 voice-chatapp-powered-by-openai
