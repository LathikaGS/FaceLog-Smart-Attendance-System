# Face-recognition-attendance-system-using-OpenCV-PyCharm-

        #Main file
        
        
        import cv2
        import os
        import pickle
        import numpy as np
        import cvzone
        import face_recognition
        import firebase_admin
        from firebase_admin import credentials
        from firebase_admin import db
        from firebase_admin import storage
        from datetime import datetime
        cred = credentials.Certificate("serviceAccountKey.json")
        firebase_admin.initialize_app(cred,{
            'databaseURL':"",
            'storageBucket':""
        })
        bucket = storage.bucket()
        cap = cv2.VideoCapture(0)
        cap.set(3, 640)
        cap.set(4, 480)
        imgBackground = cv2.imread('Resources/background.png')
        folderModePath = 'Resources/Modes'
        modePathList = os.listdir(folderModePath)
        imgModeList = []
        for path in modePathList:
            imgModeList.append(cv2.imread(os.path.join(folderModePath, path)))
        file = open('EncodeFile.p', 'rb')
        encodeListKnownWithIds = pickle.load(file)
        file.close()
        encodeListKnown, studentIds = encodeListKnownWithIds
        modeType = 0
        counter = 0
        id=0
        imgStudent =[]
        
        while True:
            success, img = cap.read()
            imgs = cv2.resize(img, (0, 0), None, 0.25, 0.25)
            imgs = cv2.cvtColor(imgs, cv2.COLOR_BGR2RGB)
            faceCurFrame = face_recognition.face_locations(imgs)
            encodeCurFrame = face_recognition.face_encodings(imgs, faceCurFrame)
            imgBackground[162:162 + 480, 55:55 + 640] = img
            imgBackground[44:44 + 633, 808:808 + 414] = imgModeList[modeType]
            for encodeface, faceLoc in zip(encodeCurFrame, faceCurFrame):
                matches = face_recognition.compare_faces(encodeListKnown, encodeface)
                facedis = face_recognition.face_distance(encodeListKnown, encodeface)
        
                matchIndex = np.argmin(facedis)
                if matches[matchIndex]:
                    y1, x2, y2, x1 = faceLoc
                    y1, x2, y2, x1 = y1 * 4, x2 * 4, y2 * 4, x1 * 4
                    bbox = 55 + x1, 162 + y1, x2 - x1, y2 - y1
                    imgBackground = cvzone.cornerRect(imgBackground, bbox, rt=0)
                    id = studentIds[matchIndex]
                    if counter == 0:
                        counter = 1
                        modeType = 1
            if counter!=0:
                if counter == 1:
                    studentInfo = db.reference(f'Students/{id}').get()
                    print(studentInfo)
                    blob = bucket.get_blob(f'Images/{id}.jpg')
                    array = np.frombuffer(blob.download_as_string(), np.uint8)
                    imgStudent = cv2.imdecode(array, cv2.COLOR_BGRA2BGR)
                    ref = db.reference(f'Students/{id}')
                    studentInfo['Attendance']+=1
                    ref.child("Attendance").set(studentInfo['Attendance'])
                if 10<counter<20:
                    modeType=2
                imgBackground[44:44 + 633, 808:808 + 414] = imgModeList[modeType]
        
                if counter<=10:
                    cv2.putText(imgBackground, str(studentInfo['Attendance']), (861, 125),
                                cv2.FONT_HERSHEY_COMPLEX, 1, (255, 255, 255), 1)
                    cv2.putText(imgBackground, str(studentInfo['Dept']), (1006, 550),
                                cv2.FONT_HERSHEY_COMPLEX, 0.5, (255, 255, 255), 1)
                    cv2.putText(imgBackground, str(id), (1006, 493),
                                cv2.FONT_HERSHEY_COMPLEX, 0.5, (255, 255, 255), 1)
                    cv2.putText(imgBackground, str(studentInfo['Batch']), (1025, 625),
                                cv2.FONT_HERSHEY_COMPLEX, 0.6, (100, 100, 100), 1)
                    cv2.putText(imgBackground, str(studentInfo['Year']), (910, 625),
                                cv2.FONT_HERSHEY_COMPLEX, 0.6, (100, 100, 100), 1)
                    cv2.putText(imgBackground, str(studentInfo['CGPA']), (1125, 625),
                                cv2.FONT_HERSHEY_COMPLEX, 0.6, (100, 100, 100), 1)
                    (w,h), _ = cv2.getTextSize(studentInfo['Name'],cv2.FONT_HERSHEY_COMPLEX,1,1)
                    offset = (414 - w)//2
                    cv2.putText(imgBackground, str(studentInfo['Name']), (808+offset, 445),
                                cv2.FONT_HERSHEY_COMPLEX, 1, (50, 50, 50), 1)
                    imgBackground[175:175+216, 909:909+216] = imgStudent
                counter+=1
        
                if counter>=20:
                    counter=0
                    modeType=0
                    studentInfo=[]
                    imgStudent=[]
                    imgBackground[44:44 + 633, 808:808 + 414] = imgModeList[modeType]
        
            cv2.imshow("Face Attendance", imgBackground)
            cv2.waitKey(1)
        
            
        #Adding data to database
        
        
        import firebase_admin
        from firebase_admin import credentials
        from firebase_admin import db
        
        cred = credentials.Certificate("serviceAccountKey.json")
        firebase_admin.initialize_app(cred,{
            'databaseURL':""
        })
        ref = db.reference('Students')
        data = {
            #Information of the students
        }
        for key, value in data.items():
            ref.child(key).set(value)
        
        
        #Encode Generator
        
        import cv2
        import face_recognition
        import pickle
        import os
        import firebase_admin
        from firebase_admin import credentials
        from firebase_admin import db
        from firebase_admin import storage
        cred = credentials.Certificate("serviceAccountKey.json")
        firebase_admin.initialize_app(cred,{
            'databaseURL': "",
            'storageBucket': ""
        })
        folderPath = 'Images'
        PathList = os.listdir(folderPath)
        imgList = []
        studentIds = []
        for path in PathList:
            imgList.append(cv2.imread(os.path.join(folderPath, path)))
            studentIds.append(os.path.splitext(path)[0])
        
            fileName = f'{folderPath}/{path}'
            bucket = storage.bucket()
            blob = bucket.blob(fileName)
            blob.upload_from_filename(fileName)
        print(studentIds)
        
        def findEncodings(imagesList):
            encodeList = []
            for img in imagesList:
                img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
                encode = face_recognition.face_encodings(img)[0]
                encodeList.append(encode)
        
            return encodeList
        encodeListKnown = findEncodings(imgList)
        encodeListKnownWithIds = [encodeListKnown, studentIds]
        #print(encodeListKnown)
        file = open("EncodeFile.p",'wb')
        pickle.dump(encodeListKnownWithIds,file)
        file.close()
