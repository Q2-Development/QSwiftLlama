# QSwiftLlama

SwiftLlama is a lightweight, Swifty wrapper around the excellent [llama.cpp](https://github.com/ggml-org/llama.cpp) inference library.  
It lets you run Llama and compatible *GGUF* models on Apple platforms (macOS, iOS, visionOS) with just a few lines of Swift.

---
## Installation
Add the package to your **Package.swift** dependencies:
```swift
.package(url: "https://github.com/Q2-Development/QSwiftLlama.git", from: "0.5.0")
```
Then declare the target dependency:
```swift
.target(
    name: "MyApp",
    dependencies: [
        .product(name: "SwiftLlama", package: "SwiftLlama")
    ]
)
```
---
## Quick-start
### 1 – Load a model
```swift
import SwiftLlama

let modelPath = "/path/to/model.gguf"            // e.g. "codellama-7b-instruct.Q4_K_S.gguf"
let llama = try SwiftLlama(modelPath: modelPath)   // throws if the model cannot be loaded
```
### 2 – Create a prompt
```swift
let prompt = Prompt.system("You are a helpful assistant.")
              .user("Write a haiku about Swift.")
```
### 3 – Generate
#### a) Blocking (async/await)
```swift
let text: String = try await llama.start(for: prompt)
print(text)
```
#### b) AsyncStream (token-by-token)
```swift
var result = ""
for try await token in await llama.start(for: prompt) {
    result += token
    print(token, terminator: "")
}
```
#### c) Combine Publisher
```swift
import Combine

var bag = Set<AnyCancellable>()

await llama.start(for: prompt)
    .sink(receiveCompletion: { completion in
        print("Finished: \(completion)")
    }, receiveValue: { delta in
        print(delta, terminator: "")
    })
    .store(in: &bag)
```
---
## Configuration
`SwiftLlama` can be fine-tuned via the `Configuration` struct:
```swift
let config = Configuration(
    nCTX:           4096,   // context window (tokens)
    temperature:    0.7,    // sampling temperature
    topK:           40,
    topP:           0.9,
    maxTokenCount:  512,    // maximum tokens generated
    stopTokens:     ["</s>"]
)
let llama = try SwiftLlama(modelPath: modelPath, modelConfiguration: config)
```
---
## Supported models
Any *GGUF* model that can be run by **llama.cpp b6098** should work.

---
## Example projects
Play with SwiftLlama in the **TestProjects** folder, you can also use our app: 
* **iOS** https://github.com/q2-development/qswiftllama

---
## Contributing
Pull requests and issues are welcome.  
Please run `swift test` before submitting a PR.

---
## License
SwiftLlama is released under the MIT license.
