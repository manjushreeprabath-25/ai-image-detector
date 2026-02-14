# ai-image-detector
   ai_detector/
├── app.py              # Flask Backend
├── static/
│   ├── css/
│   │   └── style.css   # Custom Animations
│   └── js/
│       └── script.js   # Frontend Logic (Upload & Prediction)
└── templates/
    └── index.html      # Responsive Single Page UI
    
Flask Backend (app.py)
This script handles the image upload and returns a mock prediction result.[1]

import os
import time
import random
from flask import Flask, render_template, request, jsonify
from werkzeug.utils import secure_filename

app = Flask(__name__)

# Configuration
UPLOAD_FOLDER = 'static/uploads'
ALLOWED_EXTENSIONS = {'png', 'jpg', 'jpeg'}
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

# Create upload folder if not exists
if not os.path.exists(UPLOAD_FOLDER):
    os.makedirs(UPLOAD_FOLDER)

def allowed_file(filename):
    return '.' in filename and filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/predict', methods=['POST'])
def predict():
    if 'file' not in request.files:
        return jsonify({"error": "No file uploaded"}), 400
    
  file = request.files['file']
    if file.filename == '':
        return jsonify({"error": "No file selected"}), 400

  if file and allowed_file(file.filename):
        filename = secure_filename(file.filename)
        filepath = os.path.join(app.config['UPLOAD_FOLDER'], filename)
        file.save(filepath)

   # Placeholder for AI Model Logic
  # In a real app, you would pass 'filepath' to a CNN model here.
  time.sleep(1.5)  # Simulate processing time
        
  is_ai = random.choice([True, False])
        confidence = random.randint(85, 99)
        
  result = {
            "prediction": "AI Generated" if is_ai else "Real Image",
            "confidence": f"{confidence}%",
            "image_url": f"/{filepath}",
            "warning": "High probability of synthetic manipulation detected!" if is_ai else ""
        }
        return jsonify(result)

return jsonify({"error": "Invalid file type"}), 400

if __name__ == '__main__':
    app.run(debug=True)

 **Frontend Template (templates/index.html)**
Uses Tailwind CSS for a modern, mobile-responsive layout.

    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Image Detector | Verify Authenticity</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="/static/css/style.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
</head>
<body class="bg-slate-50 font-sans text-gray-900">
<!-- Navbar -->
    <nav class="bg-white shadow-sm sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex justify-between items-center">
            <h1 class="text-2xl font-bold text-blue-600">AIDetect.io</h1>
            <div class="hidden md:flex space-x-8 text-gray-600 font-medium">
                <a href="#home" class="hover:text-blue-600">Home</a>
                <a href="#about" class="hover:text-blue-600">Problem</a>
                <a href="#upload" class="hover:text-blue-600">Verify</a>
                <a href="#cases" class="hover:text-blue-600">Use Cases</a>
            </div>
        </div>
    </nav>
<!-- Hero Section -->
    <section id="home" class="py-20 bg-gradient-to-b from-white to-slate-50 text-center px-4">
        <h2 class="text-4xl md:text-6xl font-extrabold mb-6">AI Generated Image Detection System</h2>
        <p class="text-xl text-gray-600 max-w-2xl mx-auto mb-10">
            Detect and prevent AI-generated image fraud before it causes harm. Using advanced deep-learning signatures to verify your digital world.
        </p>
        <a href="#upload" class="bg-blue-600 text-white px-8 py-4 rounded-full font-bold text-lg hover:bg-blue-700 transition transform hover:scale-105 shadow-lg">
            Upload Image <i class="fas fa-upload ml-2"></i>
        </a>
    </section>
<!-- Problem Description -->
    <section id="about" class="max-w-7xl mx-auto py-20 px-4">
        <div class="grid md:grid-cols-2 gap-12 items-center">
            <div>
                <span class="text-blue-600 font-bold uppercase tracking-widest text-sm">The Challenge</span>
                <h3 class="text-3xl font-bold mt-2 mb-6">The Rise of Synthetic Media</h3>
                <p class="text-gray-600 mb-4 text-lg">AI models like DALL-E and Midjourney can now create hyper-realistic images that are indistinguishable from real photos. While creative, this technology introduces significant risks.</p>
                <ul class="space-y-4">
                    <li class="flex items-start"><i class="fas fa-exclamation-triangle text-red-500 mt-1 mr-3"></i> <span><strong>Fraud:</strong> Creation of fake identities and documents.</span></li>
                    <li class="flex items-start"><i class="fas fa-bullhorn text-orange-500 mt-1 mr-3"></i> <span><strong>Misinformation:</strong> Spreading fake news with visual "proof."</span></li>
                    <li class="flex items-start"><i class="fas fa-shield-alt text-blue-500 mt-1 mr-3"></i> <span><strong>Objective:</strong> Our system provides a layer of trust by analyzing visual artifacts and metadata.</span></li>
                </ul>
            </div>
            <div class="bg-white p-8 rounded-2xl shadow-xl border border-gray-100">
                <img src="https://images.unsplash.com/photo-1677442136019-21780ecad995?auto=format&fit=crop&q=80&w=800" alt="AI representation" class="rounded-xl grayscale opacity-80">
            </div>
        </div>
    </section>
 <!-- Upload Section -->
    <section id="upload" class="py-20 bg-white">
        <div class="max-w-3xl mx-auto px-4">
            <div class="text-center mb-12">
                <h3 class="text-3xl font-bold">Verify Authenticity</h3>
                <p class="text-gray-600">Upload a JPG or PNG image to start analysis</p>
            </div>

  <div class="upload-card bg-slate-50 border-2 border-dashed border-gray-300 p-10 rounded-3xl text-center relative overflow-hidden transition hover:border-blue-400">
                <input type="file" id="imageInput" class="hidden" accept="image/png, image/jpeg">
                <label for="imageInput" class="cursor-pointer block">
                    <i class="fas fa-cloud-upload-alt text-5xl text-blue-500 mb-4"></i>
                    <p class="text-lg font-medium">Click to upload or drag & drop</p>
                    <p class="text-sm text-gray-500">Max file size: 5MB (PNG, JPG)</p>
                </label>
                <!-- Progress Indicator -->
                <div id="progressContainer" class="hidden mt-6">
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div id="progressBar" class="bg-blue-600 h-2.5 rounded-full" style="width: 0%"></div>
                    </div>
                    <p class="text-sm mt-2 text-blue-600 font-bold">Analyzing image signatures...</p>
                </div>
            </div>
<!-- Result Page (Hidden by Default) -->
            <div id="resultCard" class="hidden mt-12 bg-white rounded-3xl shadow-2xl overflow-hidden border border-gray-100 animate-fade-in">
                <div class="md:flex">
                    <div class="md:w-1/2 p-4">
                        <img id="previewImg" src="" class="w-full h-64 object-cover rounded-2xl shadow-inner">
                    </div>
                    <div class="md:w-1/2 p-8 flex flex-col justify-center">
                        <h4 class="text-sm uppercase tracking-widest text-gray-500 font-bold mb-2">Detection Result</h4>
                        <div id="resultLabel" class="text-3xl font-extrabold mb-4">--</div>
                        <div class="flex items-center mb-6">
                            <span class="text-gray-600 mr-3">Confidence Score:</span>
                            <span id="confidenceValue" class="text-2xl font-bold text-blue-600">0%</span>
                        </div>
                        <div id="warningMsg" class="hidden p-4 bg-red-50 text-red-700 rounded-xl border border-red-100 flex items-start">
                            <i class="fas fa-exclamation-circle mt-1 mr-3"></i>
                            <span id="warningText"></span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
    <!-- Use Cases -->
    <section id="cases" class="py-20 bg-slate-50">
        <div class="max-w-7xl mx-auto px-4">
            <h3 class="text-3xl font-bold text-center mb-16">Key Use Cases</h3>
            <div class="grid md:grid-cols-4 gap-8">
                <div class="bg-white p-8 rounded-2xl shadow-sm border border-gray-100 text-center">
                    <div class="w-16 h-16 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center mx-auto mb-6"><i class="fas fa-users text-2xl"></i></div>
                    <h4 class="font-bold mb-3">Social Media</h4>
                    <p class="text-gray-500 text-sm">Moderating fake profiles and synthetic influence campaigns.</p>
                </div>
                <div class="bg-white p-8 rounded-2xl shadow-sm border border-gray-100 text-center">
                    <div class="w-16 h-16 bg-green-100 text-green-600 rounded-full flex items-center justify-center mx-auto mb-6"><i class="fas fa-gavel text-2xl"></i></div>
                    <h4 class="font-bold mb-3">Digital Forensics</h4>
                    <p class="text-gray-500 text-sm">Verifying digital evidence for legal investigations.</p>
                </div>
                <div class="bg-white p-8 rounded-2xl shadow-sm border border-gray-100 text-center">
                    <div class="w-16 h-16 bg-purple-100 text-purple-600 rounded-full flex items-center justify-center mx-auto mb-6"><i class="fas fa-shopping-cart text-2xl"></i></div>
                    <h4 class="font-bold mb-3">Marketplaces</h4>
                    <p class="text-gray-500 text-sm">Authenticating product photos to prevent seller fraud.</p>
                </div>
                <div class="bg-white p-8 rounded-2xl shadow-sm border border-gray-100 text-center">
                    <div class="w-16 h-16 bg-orange-100 text-orange-600 rounded-full flex items-center justify-center mx-auto mb-6"><i class="fas fa-newspaper text-2xl"></i></div>
                    <h4 class="font-bold mb-3">Journalism</h4>
                    <p class="text-gray-500 text-sm">Verifying imagery source authenticity for breaking news.</p>
                </div>
            </div>
        </div>
    </section>
    <!-- Footer -->
    <footer class="bg-slate-900 text-gray-400 py-12 px-4">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center">
            <div class="mb-8 md:mb-0">
                <h2 class="text-2xl font-bold text-white mb-2">AIDetect.io</h2>
                <p class="text-sm">Final Year College Project &copy; 2024</p>
            </div>
            <div class="flex space-x-12">
                <div>
                    <h4 class="text-white font-bold mb-4 uppercase text-xs">The Team</h4>
                    <ul class="text-sm space-y-2">
                        <li>Alex Johnson</li>
                        <li>Sarah Miller</li>
                    </ul>
                </div>
                <div>
                    <h4 class="text-white font-bold mb-4 uppercase text-xs">Contact</h4>
                    <p class="text-sm">support@aidetect.edu</p>
                </div>
            </div>
        </div>
    </footer>

<script src="/static/js/script.js"></script>
</body>
</html>

**Frontend Logic (static/js/script.js)**
This script manages the file selection, progress bar animation, and API call.


document.getElementById('imageInput').addEventListener('change', function(e) {
    const file = e.target.files[0];
    if (!file) return;

  // 1. Client-side Validation
    const allowedTypes = ['image/jpeg', 'image/png', 'image/jpg'];
    if (!allowedTypes.includes(file.type)) {
        alert("Please upload a JPG or PNG file.");
        return;
    }

  // 2. Prepare UI
    const progressContainer = document.getElementById('progressContainer');
    const progressBar = document.getElementById('progressBar');
    const resultCard = document.getElementById('resultCard');
    
   progressContainer.classList.remove('hidden');
    resultCard.classList.add('hidden');
    progressBar.style.width = '0%';

  // 3. Fake Progress Bar Animation (Visual only)
    let progress = 0;
    const interval = setInterval(() => {
        progress += 10;
        progressBar.style.width = progress + '%';
        if (progress >= 100) clearInterval(interval);
    }, 100);

  // 4. API Request
    const formData = new FormData();
    formData.append('file', file);

  fetch('/predict', {
        method: 'POST',
        body: formData
    })
    .then(response => response.json())
    .then(data => {
        clearInterval(interval);
        progressBar.style.width = '100%';
        
  setTimeout(() => {
            progressContainer.classList.add('hidden');
            displayResult(data);
        }, 500);
    })
    .catch(error => {
        console.error('Error:', error);
        alert("Upload failed. Please try again.");
    });
});

function displayResult(data) {
    const resultCard = document.getElementById('resultCard');
    const previewImg = document.getElementById('previewImg');
    const resultLabel = document.getElementById('resultLabel');
    const confidenceValue = document.getElementById('confidenceValue');
    const warningMsg = document.getElementById('warningMsg');
    const warningText = document.getElementById('warningText');

  resultCard.classList.remove('hidden');
    previewImg.src = data.image_url;
    resultLabel.innerText = data.prediction;
    confidenceValue.innerText = data.confidence;

  // Styling based on result
    if (data.prediction === "AI Generated") {
        resultLabel.className = "text-3xl font-extrabold mb-4 text-red-600";
        warningMsg.classList.remove('hidden');
        warningText.innerText = data.warning;
    } else {
        resultLabel.className = "text-3xl font-extrabold mb-4 text-green-600";
        warningMsg.classList.add('hidden');
    }
}

** Custom Styles (static/css/style.css)**

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
}

.animate-fade-in {
    animation: fadeIn 0.6s ease-out forwards;
}

.upload-card:hover {
    background-color: #f1f5f9;
}

html {
    scroll-behavior: smooth;
}
    
