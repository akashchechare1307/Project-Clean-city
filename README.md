# Project-Clean-city
"A web app to help citizens report garbage by taking a photo, adding a geotag, and generating a complaint for local authorities."
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Clean City - Garbage Reporter</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.2/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
        }
        .main-container {
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        .btn-primary {
            background: linear-gradient(to right, #4facfe 0%, #00f2fe 100%);
        }
        .btn-secondary {
            background: linear-gradient(to right, #6a82fb 0%, #fc5c7d 100%);
        }
        .btn-tertiary {
             background: linear-gradient(to right, #43e97b 0%, #38f9d7 100%);
        }
        .btn-icon {
            background: linear-gradient(to right, #0acffe 0%, #495aff 100%);
        }
        #camera-stream {
            width: 100%;
            height: auto;
            max-height: 400px;
            object-fit: cover;
            border-radius: 0.75rem; /* more rounded */
        }
        .spinner {
            border-color: #4facfe;
            border-top-color: transparent;
            width: 1.25rem;
            height: 1.25rem;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen p-4">

    <div class="w-full max-w-lg bg-white/70 dark:bg-gray-800/70 main-container rounded-3xl shadow-2xl p-6 md:p-8 space-y-6 transition-all duration-500">

        <!-- Initial View -->
        <div id="initial-view">
            <div class="text-center">
                <img src="https://placehold.co/400x250/EBF8FF/4A90E2?text=Clean+City+Initiative&font=poppins" 
                     onerror="this.onerror=null; this.src='https://placehold.co/400x250/EBF8FF/4A90E2?text=Clean+City+Initiative&font=poppins';" 
                     alt="Illustration of a clean city street with recycling bins" 
                     class="w-full rounded-2xl mb-6 shadow-lg">
                <h1 class="text-3xl md:text-4xl font-bold text-gray-800 dark:text-white">Garbage Reporter</h1>
                <p class="text-gray-600 dark:text-gray-300 mt-3 text-md">Help keep your community clean. Snap a photo, add the location, and report it.</p>
            </div>
            <button id="start-report-btn" class="mt-8 w-full btn-primary text-white font-bold py-3.5 px-4 rounded-xl shadow-lg transition-transform transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-blue-300 dark:focus:ring-blue-800 flex items-center justify-center space-x-2">
                <i class="fas fa-camera-retro"></i>
                <span>Start New Report</span>
            </button>
        </div>

        <!-- Reporting View -->
        <div id="reporting-view" class="hidden space-y-6">
             <h2 class="text-2xl font-bold text-center text-gray-800 dark:text-white">Create a Report</h2>
            <div>
                <p class="text-lg font-semibold text-center mb-4 text-gray-700 dark:text-gray-300">Step 1: Capture Photo</p>
                <div id="camera-container" class="relative bg-gray-200 dark:bg-gray-700 rounded-xl overflow-hidden shadow-inner">
                    <video id="camera-stream" autoplay playsinline class="hidden"></video>
                    <div id="photo-preview-container" class="hidden text-center p-2">
                        <img id="photo-preview" class="rounded-lg max-h-64 mx-auto" alt="Captured garbage photo">
                        <p class="text-sm text-green-600 dark:text-green-400 font-medium mt-2"><i class="fas fa-check-circle"></i> Photo captured!</p>
                    </div>
                     <div id="camera-placeholder" class="p-8 text-center text-gray-500 dark:text-gray-400 flex flex-col items-center justify-center h-48">
                        <i class="fas fa-video fa-2x"></i>
                        <p class="mt-2">Camera will appear here</p>
                    </div>
                </div>
                <canvas id="photo-canvas" class="hidden"></canvas>
                <button id="capture-photo-btn" class="mt-4 w-full btn-secondary text-white font-bold py-3 px-4 rounded-xl flex items-center justify-center space-x-2 transition disabled:opacity-50 shadow-lg transform hover:scale-105" disabled>
                    <i class="fas fa-camera"></i>
                    <span>Take Photo</span>
                </button>
            </div>

            <div>
                <p class="text-lg font-semibold text-center mb-2 text-gray-700 dark:text-gray-300">Step 2: Add Details</p>
                <div id="location-status" class="bg-gray-100 dark:bg-gray-700 p-4 rounded-xl text-center text-sm font-medium shadow-inner">
                    <div class="flex items-center justify-center space-x-2">
                        <div class="spinner border-2 rounded-full"></div>
                        <span>Acquiring GPS location...</span>
                    </div>
                </div>
            </div>

            <div>
                 <label for="description" class="block mb-2 text-sm font-medium text-gray-700 dark:text-white">Description & Landmark</label>
                 <textarea id="description" rows="4" class="block p-3 w-full text-sm text-gray-900 bg-gray-50 rounded-xl border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500 shadow-inner" placeholder="e.g., 'Large pile of garbage near the main bus stop. It's been here for 3 days.'"></textarea>
            </div>
            
            <div class="flex space-x-4">
                <button id="cancel-btn" class="w-full text-center text-gray-600 bg-gray-200 hover:bg-gray-300 dark:bg-gray-600 dark:hover:bg-gray-500 font-semibold py-3 px-4 rounded-xl transition">Cancel</button>
                <button id="submit-complaint-btn" class="w-full btn-tertiary text-white font-bold py-3 px-4 rounded-xl transition disabled:opacity-50 disabled:cursor-not-allowed shadow-lg transform hover:scale-105" disabled>
                    Generate Complaint
                </button>
            </div>
        </div>

        <!-- Complaint View -->
        <div id="complaint-view" class="hidden space-y-4">
            <div class="text-center">
                 <div class="mx-auto flex items-center justify-center h-16 w-16 bg-green-100 dark:bg-green-900 rounded-full">
                    <i class="fas fa-envelope-open-text fa-2x text-green-500"></i>
                </div>
                <h2 class="text-2xl font-bold mt-4">Complaint Ready</h2>
                <p class="text-gray-600 dark:text-gray-400 mt-1">Your complaint is formatted. Send it to the relevant municipal authority.</p>
            </div>
            
            <div class="bg-gray-50 dark:bg-gray-900/50 rounded-xl p-4 shadow-inner">
                <h3 class="font-semibold mb-2 text-lg">Formatted Complaint:</h3>
                <pre id="complaint-text" class="text-sm whitespace-pre-wrap font-sans bg-white dark:bg-gray-800 p-4 rounded-lg overflow-x-auto border dark:border-gray-700"></pre>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <button id="copy-btn" class="w-full btn-icon text-white font-bold py-3 px-4 rounded-xl flex items-center justify-center space-x-2 transform hover:scale-105 shadow-lg">
                    <i class="fas fa-copy"></i>
                    <span>Copy Text</span>
                </button>
                <a id="email-btn" href="#" class="w-full btn-icon text-white font-bold py-3 px-4 rounded-xl flex items-center justify-center space-x-2 transform hover:scale-105 shadow-lg">
                     <i class="fas fa-paper-plane"></i>
                    <span>Send via Email</span>
                </a>
            </div>
            <button id="new-report-btn" class="w-full text-center font-semibold text-gray-500 hover:text-gray-700 dark:hover:text-gray-300 pt-4">Start Another Report</button>
        </div>
    </div>
    
    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-5 right-5 bg-gray-900 text-white py-2.5 px-6 rounded-xl shadow-2xl opacity-0 transition-all duration-300 transform translate-y-4">
        <p id="toast-message"></p>
    </div>

    <script>
        // DOM Elements
        const initialView = document.getElementById('initial-view');
        const reportingView = document.getElementById('reporting-view');
        const complaintView = document.getElementById('complaint-view');
        const startReportBtn = document.getElementById('start-report-btn');
        const cancelBtn = document.getElementById('cancel-btn');
        const cameraStream = document.getElementById('camera-stream');
        const photoPreview = document.getElementById('photo-preview');
        const photoPreviewContainer = document.getElementById('photo-preview-container');
        const cameraPlaceholder = document.getElementById('camera-placeholder');
        const photoCanvas = document.getElementById('photo-canvas');
        const capturePhotoBtn = document.getElementById('capture-photo-btn');
        const locationStatus = document.getElementById('location-status');
        const descriptionInput = document.getElementById('description');
        const submitComplaintBtn = document.getElementById('submit-complaint-btn');
        const complaintText = document.getElementById('complaint-text');
        const copyBtn = document.getElementById('copy-btn');
        const emailBtn = document.getElementById('email-btn');
        const newReportBtn = document.getElementById('new-report-btn');
        const toast = document.getElementById('toast');
        const toastMessage = document.getElementById('toast-message');

        // App State
        let currentStream;
        let capturedImageData = null;
        let locationData = null;

        function resetState() {
            if (currentStream) {
                currentStream.getTracks().forEach(track => track.stop());
            }
            capturedImageData = null;
            locationData = null;
            descriptionInput.value = '';
            photoPreview.src = '';
            photoPreviewContainer.classList.add('hidden');
            cameraStream.classList.add('hidden');
            cameraPlaceholder.classList.remove('hidden');
            capturePhotoBtn.disabled = true;
            capturePhotoBtn.classList.remove('hidden');
            locationStatus.innerHTML = `<div class="flex items-center justify-center space-x-2"><div class="spinner border-2 rounded-full"></div><span>Acquiring GPS location...</span></div>`;
            submitComplaintBtn.disabled = true;
            showView('initial');
        }

        function showView(viewName) {
            const views = {
                'initial': initialView,
                'reporting': reportingView,
                'complaint': complaintView
            };
            Object.values(views).forEach(view => view.classList.add('hidden'));
            if (views[viewName]) {
                views[viewName].classList.remove('hidden');
            }
        }
        
        function showToast(message) {
            toastMessage.textContent = message;
            toast.classList.remove('opacity-0', 'translate-y-4');
            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-4');
            }, 3000);
        }

        async function startCamera() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } });
                currentStream = stream;
                cameraStream.srcObject = stream;
                cameraStream.classList.remove('hidden');
                cameraPlaceholder.classList.add('hidden');
                capturePhotoBtn.disabled = false;
            } catch (error) {
                console.error("Error accessing camera:", error);
                cameraPlaceholder.innerHTML = `<p class="text-red-500 font-semibold"><i class="fas fa-exclamation-triangle"></i> Camera access denied. Please grant permission and refresh.</p>`;
            }
        }
        
        function takePhoto() {
            if (!currentStream) return;
            const video = cameraStream;
            const canvas = photoCanvas;
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
            capturedImageData = canvas.toDataURL('image/jpeg');
            photoPreview.src = capturedImageData;
            photoPreviewContainer.classList.remove('hidden');
            cameraStream.classList.add('hidden');
            currentStream.getTracks().forEach(track => track.stop());
            capturePhotoBtn.classList.add('hidden');
            checkIfReadyToSubmit();
        }

        function useMockLocation() {
            showToast("Using a random location for testing.");
            const baseLat = 28.6139, baseLon = 77.2295; // New Delhi
            const lat = baseLat + (Math.random() - 0.5) * 0.1;
            const lon = baseLon + (Math.random() - 0.5) * 0.1;
            locationData = { latitude: lat, longitude: lon, accuracy: Math.random() * 50 + 20 };
            locationStatus.innerHTML = `<div class="text-amber-600 dark:text-amber-400"><p class="font-semibold">Using Mock Location (Demo)</p><a href="https://maps.google.com/?q=${lat},${lon}" target="_blank" class="text-blue-500 hover:underline text-xs block">View on Map</a></div>`;
            checkIfReadyToSubmit();
        }

        function getLocation() {
            if (!window.isSecureContext) {
                 locationStatus.innerHTML = `<span class="text-red-500 font-semibold">Error: Geolocation requires a secure (HTTPS) connection.</span>`;
                 setTimeout(useMockLocation, 1500);
                 return;
            }
            if (!navigator.geolocation) {
                locationStatus.innerHTML = `<span class="text-red-500 font-semibold">Geolocation is not supported by your browser.</span>`;
                setTimeout(useMockLocation, 1500);
                return;
            }

            navigator.geolocation.getCurrentPosition(
                (position) => {
                    locationData = {
                        latitude: position.coords.latitude,
                        longitude: position.coords.longitude,
                        accuracy: position.coords.accuracy,
                    };
                    locationStatus.innerHTML = `<span class="text-green-600 dark:text-green-400 font-semibold"><i class="fas fa-map-marker-alt"></i> Location acquired!</span><a href="https://maps.google.com/?q=${locationData.latitude},${locationData.longitude}" target="_blank" class="text-blue-500 hover:underline text-xs block">View on Map</a>`;
                    checkIfReadyToSubmit();
                },
                (error) => {
                    console.error("Error getting location:", error);
                    let errorMessage = "Could not get location: ";
                    switch(error.code) {
                        case error.PERMISSION_DENIED: errorMessage += "Permission denied."; break;
                        case error.POSITION_UNAVAILABLE: errorMessage += "Information unavailable."; break;
                        case error.TIMEOUT: errorMessage += "Request timed out."; break;
                        default: errorMessage += "An unknown error occurred."; break;
                    }
                    locationStatus.innerHTML = `<span class="text-red-500">${errorMessage}</span>`;
                    setTimeout(useMockLocation, 1500);
                }
            );
        }
        
        function checkIfReadyToSubmit() {
            if (capturedImageData && locationData) {
                submitComplaintBtn.disabled = false;
            }
        }

        function generateComplaint() {
            if (!capturedImageData || !locationData) {
                showToast("Photo and location are required.");
                return;
            }
            if (!descriptionInput.value.trim()) {
                showToast("Please provide a description.");
                return;
            }
            
            const now = new Date();
            const complaintDate = now.toLocaleDateString('en-IN', { timeZone: 'Asia/Kolkata' });
            const complaintTime = now.toLocaleTimeString('en-IN', { timeZone: 'Asia/Kolkata', hour12: true });
            
            const { latitude, longitude } = locationData;

            const text = `To,
The Municipal Commissioner,
[Your City/Municipality Name]

Subject: Complaint regarding unattended garbage pile

Respected Sir/Madam,

This is to report an uncleared garbage pile at the location detailed below.

- Date of Report: ${complaintDate}
- Time of Report: ${complaintTime}
- Description: ${descriptionInput.value.trim()}

- Geolocation: ${latitude.toFixed(6)}, ${longitude.toFixed(6)}
- Map Link: https://maps.google.com/?q=${latitude},${longitude}

The attached photograph is for your reference. This poses a health hazard to the community. I request your immediate action to clear this.

Thank you.

Sincerely,
A Concerned Citizen`;
            complaintText.textContent = text;
            emailBtn.href = `mailto:?subject=Complaint regarding unattended garbage&body=${encodeURIComponent(text)}`;
            showView('complaint');
        }

        function copyComplaintToClipboard() {
            // Using document.execCommand('copy') for broader compatibility in restricted environments
            try {
                const complaintContent = complaintText.textContent;
                const textArea = document.createElement('textarea');
                textArea.value = complaintContent;
                document.body.appendChild(textArea);
                textArea.select();
                document.execCommand('copy');
                document.body.removeChild(textArea);
                showToast("Complaint copied to clipboard!");
            } catch (err) {
                 console.error('Failed to copy: ', err);
                showToast("Failed to copy text.");
            }
        }

        // --- Event Listeners ---
        startReportBtn.addEventListener('click', () => {
            showView('reporting');
            startCamera();
            getLocation();
        });
        cancelBtn.addEventListener('click', resetState);
        newReportBtn.addEventListener('click', resetState);
        capturePhotoBtn.addEventListener('click', takePhoto);
        submitComplaintBtn.addEventListener('click', generateComplaint);
        copyBtn.addEventListener('click', copyComplaintToClipboard);
    </script>
</body>
</html>

