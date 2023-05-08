# AI 과일 도감 만들기



<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <!-- 스타일시트 추가 링크 부분 -->
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <center>
        <br>
        <br>
        <div>Teachable Machine Image Model</div>
        <br>
        <div>사과, 토마토, 바나나를 구별하는 인공지능 모델입니다. 해당 사진을 첨부해 결과를 확인해 보세요!!</div>
        <br>
        <br>
        <!-- 코드펜에서 가져온 부분 -->
        <script class="jsbin" src="https://ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js"></script>
        <div class="container file-upload">
            <button class="file-upload-btn" type="button" onclick="$('.file-upload-input').trigger( 'click' )">Add
                Image</button>

            <div class="mt-3 container image-upload-wrap">
                <input class="file-upload-input" type='file' onchange="readURL(this);" accept="image/*" />
                <div class="drag-text">
                    <h3>Drag and drop a file or select add Image</h3>
                </div>
            </div>
            <div class="file-upload-content">
                <img class="file-upload-image" id="face-image" src="#" alt="your image" />
                <div id="label-container"></div>
                <br>
                <div class="image-title-wrap">
                    <button type="button" onclick="removeUpload()" class="remove-image">Remove 
                     
                    </button>
                </div>
            </div>
        </div>
        <!-- 코드펜에서 가져온 부분 -->

        <br>
        <br>

        <div id="label-container"></div>
    </center>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@1.3.1/dist/tf.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@0.8/dist/teachablemachine-image.min.js"></script>

    <!-- 코드펜에서 가져온 부분 -->
    <script>
        function readURL(input) {
            if (input.files && input.files[0]) {

                var reader = new FileReader();

                reader.onload = function (e) {
                    $('.image-upload-wrap').hide();

                    $('.file-upload-image').attr('src', e.target.result);
                    $('.file-upload-content').show();

                    $('.image-title').html(input.files[0].name);
                };

                reader.readAsDataURL(input.files[0]);
                init().then(() => {
                    console.log("helow")
                    predict();
                });


            } else {
                removeUpload();
            }
        }

        function removeUpload() {
            $('.file-upload-input').replaceWith($('.file-upload-input').clone());
            $('.file-upload-content').hide();
            $('.image-upload-wrap').show();
        }
        $('.image-upload-wrap').bind('dragover', function () {
            $('.image-upload-wrap').addClass('image-dropping');
        });
        $('.image-upload-wrap').bind('dragleave', function () {
            $('.image-upload-wrap').removeClass('image-dropping');
        });

    </script>
    <!-- 코드펜에서 가져온 부분 -->

    <!-- 추가 부분 : tts.js 파일 추가-->

    <script src="./tts.js"></script>

    <!-- 추가 부분 끝 -->

    <script type="text/javascript">
        // More API functions here:
        // https://github.com/googlecreativelab/teachablemachine-community/tree/master/libraries/image

        // the link to your model provided by Teachable Machine export panel
        const URL = "./my_model/";

        let model, webcam, labelContainer, maxPredictions;
        let ttsMsg = "";

        // Load the image model and setup the webcam
        async function init() {
            const modelURL = URL + "model.json";
            const metadataURL = URL + "metadata.json";

            // load the model and metadata
            // Refer to tmImage.loadFromFiles() in the API to support files from a file picker
            // or files from your local hard drive
            // Note: the pose library adds "tmImage" object to your window (window.tmImage)
            model = await tmImage.load(modelURL, metadataURL);
            maxPredictions = model.getTotalClasses();


            // append elements to the DOM
            labelContainer = document.getElementById("label-container");
            for (let i = 0; i < maxPredictions + 1; i++) { // and class labels
                labelContainer.appendChild(document.createElement("div"));
            }
        }


        // run the webcam image through the image model
        async function predict() {
            // predict can take in an image, video or canvas html element
            var image = document.getElementById("face-image")
            const prediction = await model.predict(image, false);

            // *****************************************************************************************
            // ******************                     추가 부분                     ***************************
            // ***************************************************************************************** 
            let predictIdx = 3;
            for (let i = 0; i < maxPredictions; i++) {
                if (prediction[i].probability > 0.6) {
                    predictIdx = i;
                }
            }
            let maxProbClassName = prediction[predictIdx].className;

            if (maxProbClassName == "apple") {
                ttsMsg = "사과 입니다";   //해당 내용 추가
            }
            else if (maxProbClassName == "tomato") {
                ttsMsg = "토마토 입니다";   //해당 내용 추가
            }
            else if (maxProbClassName == "banana") {
                ttsMsg = "바나나 입니다";   //해당 내용 추가
            }
            else {
            }
            speech(ttsMsg);
            labelContainer.childNodes[0].innerHTML = ttsMsg;

            // *****************************************************************************************
            // ******************                     추가 부분끝                  ***************************
            // *****************************************************************************************      

            for (let i = 0; i < maxPredictions; i++) {
                const classPrediction =
                    prediction[i].className + ": " + prediction[i].probability.toFixed(2);
                labelContainer.childNodes[i + 1].innerHTML = classPrediction;
            }
        }
    </script>

</body>

</html>
