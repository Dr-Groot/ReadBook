# ReadBook

![1024](https://user-images.githubusercontent.com/63160825/220829072-f6763d48-c635-4634-a481-35fe113911a3.png)

Read Book is an iOS application which helps us to recognise text from an image and even you can play that text and copy it for further use. I have used Vision for Text Recognition and AVFoundation for speech.

![Screenshot 2023-02-23 at 11 22 38 AM](https://user-images.githubusercontent.com/63160825/220830449-58a84b0f-25ac-498b-bbe1-952319f613b7.png)

And when you select any image and click on convert, you will get the text like this and you can even play that text as shown below:

![IMG_2188](https://user-images.githubusercontent.com/63160825/220830678-8902e882-2db1-45c3-acab-fe3757133bc9.PNG)

## Important Codes:

For recognising the text from any image first you have to import vison and convert the image into cgImage type. Then make a handler of **VNImageRequestHandler** type and a request of **VNRecognizeTextRequest** type. After that assign observation to request and extract text from the observation and ask handler to handle the request. You can even add some request property that how text should be extracted like, recognitionLanguage, recognitionLevel, etc.

```swift
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
