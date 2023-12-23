# FaceLog: Smart Attendance System

Welcome to FaceCloud, a sophisticated attendance system that leverages facial recognition technology for accurate identification and integrates seamlessly with the cloud for efficient attendance management.

## Overview

FaceCloud combines the power of the dlib 64 model and OpenCV to provide precise facial recognition, ensuring a secure and reliable attendance tracking experience. With Firebase Realtime Database integration, FaceCloud offers real-time updates and accessibility from anywhere, transforming attendance management into a streamlined and modern process.

## Features

- **Advanced Facial Recognition:** Utilizes the dlib 64 model and OpenCV for high-precision facial recognition, ensuring accuracy in attendance tracking.

- **Cloud Integration:** Seamlessly connects with Firebase Realtime Database, enabling real-time updates and providing convenient access to attendance records from any device.

- **Efficient Attendance Management:** Reduces manual effort by automating the attendance tracking process and enhances accessibility through cloud integration.

## Getting Started

### Prerequisites

- Python 3.x
- OpenCV
- dlib
- Firebase account with Realtime Database

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/LathikaGS/FaceLog-Smart-Attendance-System.git
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Configure Firebase credentials:
   - Create a Firebase project and obtain the necessary credentials.
   - Update the Firebase configuration in the `main.py`

4. Run the application:
   ```bash
   python main.py
   ```

## Usage

1. Launch the application and enroll faces for recognition.
2. The system will automatically recognize faces during attendance sessions.
3. Attendance records are instantly updated in the Firebase Realtime Database.

## Contributing

Feel free to contribute to the development of FaceCloud.


## Acknowledgments

- Special thanks to the contributors of the dlib and OpenCV projects for their excellent libraries.
- Firebase for providing a robust cloud infrastructure.

Happy coding!
