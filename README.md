# ReadBook

![1024](https://user-images.githubusercontent.com/63160825/220829072-f6763d48-c635-4634-a481-35fe113911a3.png)

Read Book is an iOS application which helps us to recognise text from an image and even you can play that text and copy it for further use. I have used Vision for Text Recognition and AVFoundation for speech.

![Screenshot 2023-02-23 at 11 22 38 AM](https://user-images.githubusercontent.com/63160825/220830449-58a84b0f-25ac-498b-bbe1-952319f613b7.png)

And when you select any image and click on convert, you will get the text like this and you can even play that text as shown below:

![IMG_2188](https://user-images.githubusercontent.com/63160825/220830678-8902e882-2db1-45c3-acab-fe3757133bc9.PNG)

## Important Codes:

For recognising the text from any image first you have to import vison and convert the image into cgImage type. Then make a handler of **VNImageRequestHandler** type and a request of **VNRecognizeTextRequest** type. After that assign observation to request and extract text from the observation and ask handler to handle the request. You can even add some request property that how text should be extracted like, recognitionLanguage, recognitionLevel, etc.

```swift
  import Vision
  
  func requestText() {
        guard let cgImage = self.recievedImage?.cgImage else { return }
        let handler = VNImageRequestHandler(cgImage: cgImage, options: [:])
        var request = VNRecognizeTextRequest(completionHandler: nil)
        var text = ""

        request = VNRecognizeTextRequest(completionHandler: {(request, error) in
            guard let observations = request.results as? [VNRecognizedTextObservation] else { fatalError("Invalid ovservation")}

            for observation in observations {
                guard let topCandidate = observation.topCandidates(1).first else {
                    print("Not candidate")
                    continue
                }
                text += "\n\(topCandidate.string)"
            }
            DispatchQueue.main.async {
                self.imageTextView.text = text
            }
        })

        request.customWords = ["custOm"]
        request.minimumTextHeight = 0.03125
        request.recognitionLevel = .accurate
        request.recognitionLanguages = ["en_US"]
        request.usesLanguageCorrection = true

        let requests = [request]

        DispatchQueue.global(qos: .userInitiated).async {
            try?handler.perform(requests)
        }
    }
```

For speech we use AVFoundation and its **AVSpeechSynthesizer** and also in this you can set the speech property as you like. For ex: voice, rate, etc.

```swift  
  import AVFoundation
  
  let synthesizer = AVSpeechSynthesizer()
  
  func requestSound(text: String) {
        let utterance = AVSpeechUtterance(string: text)
        utterance.voice = AVSpeechSynthesisVoice(language: "en-GB")
        utterance.rate = 0.5

        synthesizer.speak(utterance)
    }
```

> We can even recognise text using CoreML and GoogleMLKit/TextRecognition

Basically Text Recognition is a part of OCR. OCR stands for Optical character recognition or optical character reader. OCR Will scan the document or image file and then converting the text into a machine-readable.

![image](https://user-images.githubusercontent.com/63160825/220832651-8009174d-0ef6-477d-ba8b-4dddccbc7f83.png)

let me break process one by one and explain you

**Image Acquisition**

In this process, an Image/ document will be scanned and replace each pixel in an image with a black or a white pixel
Example Image:

![image](https://user-images.githubusercontent.com/63160825/220832739-1bb79391-ea17-4c47-902b-461cbd1f5cf2.png)


**Pre-processing**

Areas outside the text will be removed
Example Image:

![image](https://user-images.githubusercontent.com/63160825/220832856-2f7802ad-f50a-4295-adca-423587d92be6.png)

After Pre-processing that black and white image we will get like the above image.


**Segmentation**

![image](https://user-images.githubusercontent.com/63160825/220832992-5a037e18-df6b-4148-ad3d-799c90b9927b.png)

Just look at the 22 it was like joined with one and other , So in this process OCR will segmenting these type

Feature Extraction:

+ In this process each and every character will be Recognize and convert as machine-readable text
+ OCR have many font will compare and convert it
+ There are many Approach, will show some two

**Approach #1**

Will scan by single, single character and compare with functions

![image](https://user-images.githubusercontent.com/63160825/220833158-00f6e63c-3f2f-4c29-984c-1b3491be67ba.png)

**Approach #2**

In this Approach will take line by line (Like Human eyes reading )and will convert it

![image](https://user-images.githubusercontent.com/63160825/220833264-ddde483e-c682-40b4-aff6-c1fe9a482589.png)

Like this there are many Approach, Its based on what tech we need


**Post-Processing**

Computer also do some mistake (OCR make some spelling mistake while recognition), So here will try to correct it.
