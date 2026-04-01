// ============================================
// PWA INSTALL SCRIPT untuk DOTOPUP
// Copy dan paste ini ke dalam tag <script> di file HTML Anda
// ============================================

// Variabel untuk menyimpan deferred prompt
let deferredPrompt = null;
let installBanner = null;

// ============================================
// 1. DETECT PWA INSTALL AVAILABILITY
// ============================================
function checkPWAInstallable() {
  // Cek apakah browser mendukung PWA install
  const isInstallable = 'beforeinstallprompt' in window;
  
  // Cek apakah sudah terinstall
  const isInstalled = window.matchMedia('(display-mode: standalone)').matches ||
                      window.navigator.standalone === true ||
                      document.referrer.includes('android-app://');
  
  return {
    isInstallable,
    isInstalled,
    canShowPrompt: isInstallable && !isInstalled
  };
}

// ============================================
// 2. HANDLE BEFOREINSTALLPROMPT EVENT
// ============================================
window.addEventListener('beforeinstallprompt', (e) => {
  // Mencegah prompt native muncul otomatis
  e.preventDefault();
  
  // Simpan event untuk nanti dipanggil manual
  deferredPrompt = e;
  
  // Tampilkan banner install
  showInstallBanner();
  
  console.log('PWA install prompt available');
});

// ============================================
// 3. HANDLE APP INSTALLED EVENT
// ============================================
window.addEventListener('appinstalled', () => {
  // Sembunyikan banner setelah app terinstall
  hideInstallBanner();
  
  // Reset deferred prompt
  deferredPrompt = null;
  
  // Tampilkan pesan sukses
  showToast('✅ Aplikasi berhasil diinstall!');
  
  console.log('PWA app installed');
});

// ============================================
// 4. SHOW INSTALL BANNER
// ============================================
function showInstallBanner() {
  const pwaStatus = checkPWAInstallable();
  
  if (!pwaStatus.canShowPrompt) {
    console.log('PWA install tidak tersedia atau sudah terinstall');
    return;
  }
  
  // Cari atau buat banner
  installBanner = document.getElementById('installBanner');
  
  if (installBanner) {
    installBanner.classList.remove('is-hidden');
    installBanner.style.display = 'flex';
    
    // Animasi slide-in
    installBanner.style.animation = 'slideUp 0.5s ease-out';
  }
}

// ============================================
// 5. HIDE INSTALL BANNER
// ============================================
function hideInstallBanner() {
  if (installBanner) {
    installBanner.classList.add('is-hidden');
    installBanner.style.display = 'none';
  }
}

// ============================================
// 6. PROMPT INSTALL (DIPANGGIL SAAT USER CLICK)
// ============================================
async function promptInstall() {
  if (!deferredPrompt) {
    // Jika tidak ada deferred prompt, tampilkan modal manual
    openInstallAppModal();
    return;
  }
  
  try {
    // Tampilkan prompt install native
    deferredPrompt.prompt();
    
    // Tunggu user merespon
    const { outcome } = await deferredPrompt.userChoice;
    
    console.log(`User response to install prompt: ${outcome}`);
    
    if (outcome === 'accepted') {
      console.log('User accepted install prompt');
      showToast('📱 Sedang menginstall aplikasi...');
      hideInstallBanner();
    } else {
      console.log('User dismissed install prompt');
    }
    
    // Reset deferred prompt (hanya bisa dipanggil sekali)
    deferredPrompt = null;
    
  } catch (error) {
    console.error('Error prompting install:', error);
    // Fallback ke modal manual
    openInstallAppModal();
  }
}

// ============================================
// 7. SHOW TOAST MESSAGE
// ============================================
function showToast(message) {
  // Buat toast element jika belum ada
  let toast = document.getElementById('pwaToast');
  
  if (!toast) {
    toast = document.createElement('div');
    toast.id = 'pwaToast';
    toast.className = 'fixed bottom-20 sm:bottom-4 left-4 right-4 sm:left-auto sm:right-4 sm:w-80 bg-gray-900 text-white px-4 py-3 rounded-lg shadow-lg transform translate-y-full opacity-0 transition-all duration-300 z-[1100]';
    document.body.appendChild(toast);
  }
  
  toast.textContent = message;
  toast.classList.remove('translate-y-full', 'opacity-0');
  
  setTimeout(() => {
    toast.classList.add('translate-y-full', 'opacity-0');
  }, 3000);
}

// ============================================
// 8. CHECK ON PAGE LOAD
// ============================================
function checkInstallStatusOnLoad() {
  const pwaStatus = checkPWAInstallable();
  
  console.log('PWA Status:', pwaStatus);
  
  // Jika sudah terinstall, sembunyikan banner
  if (pwaStatus.isInstalled) {
    hideInstallBanner();
    console.log('App already installed');
  }
  
  // Jika bisa diinstall, tampilkan banner
  if (pwaStatus.canShowPrompt) {
    // Delay sedikit biar UX lebih smooth
    setTimeout(() => {
      showInstallBanner();
    }, 2000);
  }
}

// ============================================
// 9. MANUAL INSTALL INSTRUCTIONS
// ============================================
function getInstallInstructions() {
  const userAgent = navigator.userAgent.toLowerCase();
  const isIOS = /iphone|ipad|ipod/.test(userAgent);
  const isAndroid = /android/.test(userAgent);
  const isChrome = /chrome/.test(userType) && !/edge/.test(userAgent);
  const isSafari = /safari/.test(userAgent) && !/chrome/.test(userAgent);
  
  if (isIOS && isSafari) {
    return {
      platform: 'iOS',
      steps: [
        'Ketuk tombol Share (↑) di bawah layar',
        'Gulir ke bawah dan ketuk "Add to Home Screen"',
        'Ketuk "Add" di pojok kanan atas'
      ]
    };
  }
  
  if (isAndroid && isChrome) {
    return {
      platform: 'Android',
      steps: [
        'Ketuk titik tiga (⋮) di pojok kanan atas',
        'Ketuk "Add to Home screen" atau "Install app"',
        'Konfirmasi dengan ketuk "Install"'
      ]
    };
  }
  
  return {
    platform: 'Desktop',
    steps: [
      'Ketuk icon install (⊕) di address bar browser',
      'Ketuk "Install" untuk menambahkan ke desktop'
    ]
  };
}

// ============================================
// 10. EXPORT FUNGSI (untuk global access)
// ============================================
window.PWAInstall = {
  checkStatus: checkPWAInstallable,
  showBanner: showInstallBanner,
  hideBanner: hideInstallBanner,
  promptInstall: promptInstall,
  getInstructions: getInstallInstructions
};

// ============================================
// 11. INITIALIZE ON DOM READY
// ============================================
if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', checkInstallStatusOnLoad);
} else {
  checkInstallStatusOnLoad();
}

// ============================================
// CSS ANIMATIONS (otomatis ditambahkan jika belum ada)
// ============================================
const style = document.createElement('style');
style.textContent = `
  @keyframes slideUp {
    from {
      opacity: 0;
      transform: translateY(20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
  
  .slide-up {
    animation: slideUp 0.3s ease-out;
  }
  
  .install-banner {
    background: linear-gradient(135deg, #10b981 0%, #06b6d4 100%);
    color: white;
    padding: 12px 16px;
    border-radius: 12px;
    margin-bottom: 16px;
    display: flex;
    align-items: center;
    gap: 12px;
    cursor: pointer;
    transition: all 0.3s;
    box-shadow: 0 4px 15px rgba(16, 185, 129, 0.3);
  }
  
  .install-banner:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 25px rgba(16, 185, 129, 0.4);
  }
  
  .install-banner-icon {
    width: 48px;
    height: 48px;
    background: white;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
  }
  
  .install-banner-icon svg {
    width: 28px;
    height: 28px;
    color: #10b981;
  }
`;

document.head.appendChild(style);
