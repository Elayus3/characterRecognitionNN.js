var mainCanvas = document.getElementById('main');
var mainCtx = mainCanvas.getContext("2d");

var weightCanvas = document.getElementById('weightMatrix');
var weightCtx = weightCanvas.getContext("2d");

var guessCanvas = document.getElementById('guessing');
var guessCtx = guessCanvas.getContext("2d");

var trainingA = true, trainingB = false, trainingC = false;
var trainedA = 0, trainedB = 0, trainedC = 0;
var weightMatrixA = [], weightMatrixB = [], weightMatrixC = [], candidateA = [], candidateB = [], candidateC = [], inputMatrix = [], retouche = [];

var trainA = document.getElementById('trainA');
trainA.addEventListener("click", trainAf);
var trainB = document.getElementById('trainB');
trainB.addEventListener("click", trainBf);
var trainC = document.getElementById('trainC');
trainC.addEventListener("click", trainCf);

var guessButton = document.getElementById("guess");
guessButton.addEventListener("click", guess);

var canvasWidth = 500, canvasHeight = 500, cubeSize = 50;

for (var i = 0; i < canvasWidth / cubeSize; i++) {
    weightMatrixA[i] = [];
    weightMatrixB[i] = [];
    weightMatrixC[i] = [];
    candidateA[i] = [];
    candidateB[i] = [];
    candidateC[i] = [];
    inputMatrix[i] = [];
    retouche[i] = [];
    for (var j = 0; j < canvasHeight / cubeSize; j++) {
        weightMatrixA[i][j] = 0;
        weightMatrixB[i][j] = 0;
        weightMatrixC[i][j] = 0;
        candidateA[i][j] = 0;
        candidateB[i][j] = 0;
        candidateC[i][j] = 0;
        inputMatrix[i][j] = 0;
        retouche[i][j] = 0;
    }
}


if (mainCanvas.addEventListener) {
    mainCanvas.addEventListener("mousedown", drawRect);
} else if (mainCanvas.attachEvent) {
    mainCanvas.attachEvent("onmousedown", drawRect);
}
if (mainCanvas.addEventListener) {
    mainCanvas.addEventListener("mousemove", dragPaint);
} else if (mainCanvas.attachEvent) {
    mainCanvas.attachEvent("onmousemove", dragPaint);
}
if (mainCanvas.addEventListener) {
    mainCanvas.addEventListener("mouseup", stap);
} else if (mainCanvas.attachEvent) {
    mainCanvas.attachEvent("onmouseup", stap);
}



if (guessCanvas.addEventListener) {
    guessCanvas.addEventListener("mousedown", drawRect1);
} else if (guessCanvas.attachEvent) {
    guessCanvas.attachEvent("onmousedown", drawRect1);
}
if (guessCanvas.addEventListener) {
    guessCanvas.addEventListener("mousemove", dragPaint1);
} else if (guessCanvas.attachEvent) {
    guessCanvas.attachEvent("onmousemove", dragPaint1);
}
if (guessCanvas.addEventListener) {
    guessCanvas.addEventListener("mouseup", stap1);
} else if (guessCanvas.attachEvent) {
    guessCanvas.attachEvent("onmouseup", stap1);
}


var painting = false;
var inputPainting = false;

function drawRect(e) {

    var rect = mainCanvas.getBoundingClientRect();
    var x = e.clientX - rect.left;
    var y = e.clientY - rect.top;

    x = x - x % cubeSize;
    y = y - y % cubeSize;

    if (trainingA && retouche[x / cubeSize][y / cubeSize] == 0) {
        weightMatrixA[x / cubeSize][y / cubeSize]++;
        retouche[x / cubeSize][y / cubeSize]++;
    } else if (trainingB && retouche[x / cubeSize][y / cubeSize] == 0) {
        weightMatrixB[x / cubeSize][y / cubeSize]++;
        retouche[x / cubeSize][y / cubeSize]++;
    } else if (trainingC && retouche[x / cubeSize][y / cubeSize] == 0) {
        weightMatrixC[x / cubeSize][y / cubeSize]++;
        retouche[x / cubeSize][y / cubeSize]++;
    }
    paintWeight();

    mainCtx.fillStyle = "#0080FF"
    mainCtx.fillRect(x, y, cubeSize, cubeSize);
    painting = true;
}


function dragPaint(e) {
    var rect = mainCanvas.getBoundingClientRect();
    var x = e.clientX - rect.left;
    var y = e.clientY - rect.top;
    if (!(x < canvasWidth && y < canvasHeight && x > 0 && y > 0))
        painting = false;
    if (painting) {
        x = x - x % cubeSize;
        y = y - y % cubeSize;

        if (trainingA && retouche[x / cubeSize][y / cubeSize] == 0) {
            weightMatrixA[x / cubeSize][y / cubeSize]++;
            retouche[x / cubeSize][y / cubeSize]++;
        } else if (trainingB && retouche[x / cubeSize][y / cubeSize] == 0) {
            weightMatrixB[x / cubeSize][y / cubeSize]++;
            retouche[x / cubeSize][y / cubeSize]++;
        } else if (trainingC && retouche[x / cubeSize][y / cubeSize] == 0) {
            weightMatrixC[x / cubeSize][y / cubeSize]++;
            retouche[x / cubeSize][y / cubeSize]++;
        }
        paintWeight();

        mainCtx.fillStyle = "#0080FF"
        mainCtx.fillRect(x, y, cubeSize, cubeSize);
    }

}

function stap() {
    painting = false;
}

function paintWeight() {
    weightCtx.clearRect(0, 0, canvasWidth, canvasHeight);
    weightCtx.font = "20px Georgia";

    for (var i = 0; i < canvasWidth / cubeSize; i++) {
        weightCtx.beginPath();
        weightCtx.moveTo(i * cubeSize, 0);
        weightCtx.lineTo(i * cubeSize, canvasHeight);
        weightCtx.stroke();

        weightCtx.beginPath();
        weightCtx.moveTo(0, i * cubeSize);
        weightCtx.lineTo(canvasWidth, i * cubeSize);
        weightCtx.stroke();

        for (var j = 0; j < canvasHeight / cubeSize; j++) {
            if (trainingA) {
                weightCtx.fillText(" " + weightMatrixA[i][j] + " ", i * cubeSize + 15, j * cubeSize + 30);
            } else if (trainingB) {
                weightCtx.fillText(" " + weightMatrixB[i][j] + " ", i * cubeSize + 15, j * cubeSize + 30);
            } else if (trainingC) {
                weightCtx.fillText(" " + weightMatrixC[i][j] + " ", i * cubeSize + 15, j * cubeSize + 30);
            }
        }
    }


}

function trainAf() {
    trainingA = true;
    trainingB = false;
    trainingC = false;
    trainedA++;
    trainAgain();
    paintWeight();
}
function trainBf() {
    trainingA = false;
    trainingB = true;
    trainingC = false;
    trainedB++;
    trainAgain();
    paintWeight();
}
function trainCf() {
    trainingA = false;
    trainingB = false;
    trainingC = true;
    trainedC++;
    trainAgain();
    paintWeight();
}

function trainAgain() {
    for (var i = 0; i < canvasWidth / cubeSize; i++) {
        retouche[i] = [];
        for (var j = 0; j < canvasHeight / cubeSize; j++) {
            retouche[i][j] = 0;
        }
    }
    mainCtx.clearRect(0, 0, canvasWidth, canvasHeight);
}



function guess() {
    var probA = 0, probB = 0, probC = 0;
    var ticticA=0,ticticB=0,ticticC=0;

    for (var i = 0; i < canvasWidth / cubeSize; i++) {
        for (var j = 0; j < canvasHeight / cubeSize; j++) {
            candidateA[i][j] = weightMatrixA[i][j] * inputMatrix[i][j];
            candidateB[i][j] = weightMatrixB[i][j] * inputMatrix[i][j];
            candidateC[i][j] = weightMatrixC[i][j] * inputMatrix[i][j];
        }
    }

    for (var i = 0; i < canvasWidth / cubeSize; i++) {
        for (var j = 0; j < canvasHeight / cubeSize; j++) {
            probA += candidateA[i][j];
            probB += candidateB[i][j];
            probC += candidateC[i][j];
            ticticA += weightMatrixA[i][j];
            ticticB += weightMatrixB[i][j];
            ticticC += weightMatrixC[i][j];
        }
    }
    probA/=ticticA;
    probB/=ticticB;
    probC/=ticticC;

    var largest = probA, largestInd = 'A';
    if (largest < probB) {
        largest = probB;
        largestInd = 'B';
    }
    if (largest < probC) {
        largest = probC;
        largestInd = 'C';
    }
    alert("HAS TO BEEEEEEEEEEEEEEEEEEEE: "+largestInd);
    guessCtx.clearRect(0,0,canvasWidth,canvasHeight);
    for (var i = 0; i < canvasWidth / cubeSize; i++) {
        for (var j = 0; j < canvasHeight / cubeSize; j++) {
            inputMatrix[i][j] = 0;
        }
    }
}



function drawRect1(e) {

    var rect = guessCanvas.getBoundingClientRect();
    var x = e.clientX - rect.left;
    var y = e.clientY - rect.top;

    x = x - x % cubeSize;
    y = y - y % cubeSize;

    if (inputMatrix[x / cubeSize][y / cubeSize] == 0)
        inputMatrix[x / cubeSize][y / cubeSize] = 1;

    guessCtx.fillStyle = "#0080FF"
    guessCtx.fillRect(x, y, cubeSize, cubeSize);
    inputPainting = true;
}

function dragPaint1(e) {
    var rect = guessCanvas.getBoundingClientRect();
    var x = e.clientX - rect.left;
    var y = e.clientY - rect.top;
    if (!(x < canvasWidth && y < canvasHeight && x > 0 && y > 0))
        inputPainting = false;

    if (inputPainting) {
        x = x - x % cubeSize;
        y = y - y % cubeSize;

        if (inputMatrix[x / cubeSize][y / cubeSize] == 0) {
            inputMatrix[x / cubeSize][y / cubeSize]++;
        }

        guessCtx.fillStyle = "#0080FF"
        guessCtx.fillRect(x, y, cubeSize, cubeSize);
    }

}

function stap1() {
    inputPainting = false;
}