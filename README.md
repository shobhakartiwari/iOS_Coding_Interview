# Company-Wise Technical Interview Questions
- 🎓 Welcome, I’m Shobhakar Tiwari working in the USA 🇺🇸 and currently in the Central Standard Time Zone (CST).  I enjoy writing blogs on Medium, contributing to the iOS dev community, and sharing iOS interview questions to support junior and senior iOS developers alike.
  
- [If you preparing for iOS Interview feel free to connect for mentorship.](https://www.linkedin.com/in/shobhakar-tiwari/)

- A comprehensive collection of real-world technical interview questions and scenarios from leading technology companies. This repository helps developers prepare for coding rounds, system design interviews, and technical assessments.

## **Walmart Interview Question**
- [Walmart Interview Question](#walmart-interview-question)
  - [Problem Statement](#problem-statement)
  - [Technical Requirements](#technical-requirements)
  - [Implementation Guide](#implementation-guide)
  - [Code Solution](#code-solution)
  - [Technical Deep Dive](#technical-deep-dive)

### Problem Statement
**Real-time ETA Tracking in Ride-Sharing Application**

Design and implement a feature for a ride-sharing application that displays real-time ETA (Estimated Time of Arrival) updates for drivers. The system must efficiently handle periodic network requests while maintaining optimal resource usage and user experience.

### Technical Requirements
1. Implement periodic ETA fetching (10-second intervals)
2. Handle view lifecycle management
3. Implement proper network request management
4. Ensure thread-safe UI updates
5. Implement memory leak prevention
6. Handle error cases and network failures

### Implementation Guide

#### Architecture Components
- **Timer System**: Manages periodic network requests
- **Network Layer**: Handles API communication
- **UI Layer**: Updates and manages view state
- **Error Handling**: Implements retry mechanism with exponential backoff

#### Key Considerations
- Resource optimization during screen transitions
- Thread safety in UI updates
- Memory management
- Network failure resilience

### Code Solution

```swift
import UIKit

class ETAViewController: UIViewController {
    // MARK: - Properties
    private var timer: Timer?
    private let etaURL = URL(string: "https://api.example.com/driver/eta")!
    
    // MARK: - Lifecycle Methods
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        startFetchingETA()
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        stopFetchingETA()
    }
    
    // MARK: - Private Methods
    private func startFetchingETA() {
        timer = Timer.scheduledTimer(
            timeInterval: 10.0,
            target: self,
            selector: #selector(fetchETA),
            userInfo: nil,
            repeats: true
        )
        timer?.fire()
    }
    
    private func stopFetchingETA() {
        timer?.invalidate()
        timer = nil
    }
    
    @objc private func fetchETA() {
        let task = URLSession.shared.dataTask(with: etaURL) { [weak self] data, response, error in
            guard let self = self else { return }
            
            if let error = error {
                print("Failed to fetch ETA: \(error)")
                return
            }
            
            guard let data = data else { return }
            if let eta = self.parseETA(from: data) {
                DispatchQueue.main.async {
                    self.updateUI(with: eta)
                }
            }
        }
        task.resume()
    }
    
    private func parseETA(from data: Data) -> String? {
        return String(data: data, encoding: .utf8)
    }
    
    private func updateUI(with eta: String) {
        print("ETA Updated: \(eta)")
    }
}
```

### Technical Deep Dive

#### 1. Network Failure Handling
Implement an exponential backoff strategy for handling network failures:

```swift
private func retryRequest(after delay: TimeInterval) {
    DispatchQueue.main.asyncAfter(deadline: .now() + delay) { [weak self] in
        self?.fetchETA()
    }
}
```

#### 2. Thread Safety Implementation
Ensure UI updates occur on the main thread:

```swift
DispatchQueue.main.async {
    self.updateUI(with: eta)
}
```

#### 3. Request Cancellation
Implement proper request cancellation to prevent resource leaks:

```swift
private var currentTask: URLSessionDataTask?

@objc private func fetchETA() {
    currentTask?.cancel()
    currentTask = URLSession.shared.dataTask(with: etaURL) { [weak self] data, response, error in
        // Handle response
    }
    currentTask?.resume()
}
```

## Contributing
We welcome contributions! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

## All rights @Shobhakar Tiwari
