<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Batch File Renamer</title>

<!-- JSZip CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>

<style>
body {
    font-family: Arial;
    background:#f4f4f4;
    padding:30px;
}
.container {
    max-width:700px;
    margin:auto;
    background:white;
    padding:20px;
    border-radius:8px;
}
h1 { margin-top:0 }
input, button {
    padding:10px;
    margin:5px 0;
}
#fileList {
    height:200px;
    overflow:auto;
    border:1px solid #ddd;
    padding:10px;
    background:#fafafa;
}
.ad {
    margin:20px 0;
    padding:15px;
    background:#eee;
    text-align:center;
}
</style>
</head>
<body>

<div class="container">
<h1>Batch File Renamer</h1>

<input type="file" id="fileInput" multiple>
<br>

<input type="text" id="pattern" placeholder="file_###">
<br>

<button onclick="preview()">Preview</button>
<button onclick="downloadZip()">Download ZIP</button>

<div id="fileList"></div>

<div class="ad">
<!-- AD SPACE -->
Google AdSense
</div>

</div>

<script>
let files = [];

document.getElementById("fileInput").addEventListener("change", e => {
    files = Array.from(e.target.files);
    showOriginal();
});

function showOriginal(){
    const list = document.getElementById("fileList");
    list.innerHTML = "";
    files.forEach(f => {
        list.innerHTML += f.name + "<br>";
    });
}

function formatNumber(n, digits){
    return String(n).padStart(digits,"0");
}

function generateName(index, pattern, ext){
    const hashes = (pattern.match(/#/g) || []).length;
    const num = formatNumber(index, hashes);
    return pattern.replace("#".repeat(hashes), num) + ext;
}

function preview(){
    const pattern = document.getElementById("pattern").value;
    if(!pattern) return;

    const list = document.getElementById("fileList");
    list.innerHTML = "";

    files.forEach((f,i)=>{
        const ext = "." + f.name.split(".").pop();
        const newName = generateName(i+1, pattern, ext);
        list.innerHTML += newName + "<br>";
    });
}

async function downloadZip(){
    const pattern = document.getElementById("pattern").value;
    if(!pattern) return;

    const zip = new JSZip();

    for(let i=0;i<files.length;i++){
        const f = files[i];
        const ext = "." + f.name.split(".").pop();
        const newName = generateName(i+1, pattern, ext);

        const data = await f.arrayBuffer();
        zip.file(newName, data);
    }

    const blob = await zip.generateAsync({type:"blob"});
    const a = document.createElement("a");
    a.href = URL.createObjectURL(blob);
    a.download = "renamed_files.zip";
    a.click();
}
</script>

</body>
</html>
