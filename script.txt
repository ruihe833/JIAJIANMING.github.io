// 页面加载完成后执行
document.addEventListener('DOMContentLoaded', function() {
    // 移动端导航菜单
    const hamburger = document.querySelector('.hamburger');
    const navMenu = document.querySelector('.nav-menu');
    
    hamburger.addEventListener('click', function() {
        hamburger.classList.toggle('active');
        navMenu.classList.toggle('active');
    });
    
    // 点击导航链接关闭移动端菜单
    document.querySelectorAll('.nav-link').forEach(n => n.addEventListener('click', () => {
        hamburger.classList.remove('active');
        navMenu.classList.remove('active');
    }));
    
    // 平滑滚动
    document.querySelectorAll('a[href^="#"]').forEach(anchor => {
        anchor.addEventListener('click', function (e) {
            e.preventDefault();
            const target = document.querySelector(this.getAttribute('href'));
            if (target) {
                target.scrollIntoView({
                    behavior: 'smooth',
                    block: 'start'
                });
            }
        });
    });
    
    // 导航栏高亮
    window.addEventListener('scroll', function() {
        let current = '';
        const sections = document.querySelectorAll('section');
        
        sections.forEach(section => {
            const sectionTop = section.offsetTop;
            const sectionHeight = section.clientHeight;
            if (scrollY >= (sectionTop - 200)) {
                current = section.getAttribute('id');
            }
        });
        
        document.querySelectorAll('.nav-link').forEach(link => {
            link.classList.remove('active');
            if (link.getAttribute('href') === '#' + current) {
                link.classList.add('active');
            }
        });
    });
    
    // 滚动动画
    const observerOptions = {
        threshold: 0.1,
        rootMargin: '0px 0px -50px 0px'
    };
    
    const observer = new IntersectionObserver(function(entries) {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                entry.target.style.opacity = '1';
                entry.target.style.transform = 'translateY(0)';
            }
        });
    }, observerOptions);
    
    // 观察所有需要动画的元素
    document.querySelectorAll('.service-card, .platform-item, .contact-card').forEach(el => {
        el.style.opacity = '0';
        el.style.transform = 'translateY(30px)';
        el.style.transition = 'opacity 0.6s ease, transform 0.6s ease';
        observer.observe(el);
    });
});

// 下载名片功能
function downloadVCard() {
    const vCardData = `BEGIN:VCARD
VERSION:3.0
FN:贾建明
FN;CHARSET=UTF-8:JIA JIAN MING
ORG:九龙坡区质鼎汽车配件营业部
TITLE:总经理
TEL:+86 138-9619-4029
EMAIL:jiaxiao888888@outlook.com
ADR:;;重庆市九龙坡区石桥铺街道石新路216号附84号;;重庆;400039;中国
NOTE:跨境电商代运营专家，专注Amazon、eBay、Shopee等平台运营
URL:https://www.zhiding-auto.com
END:VCARD`;

    const blob = new Blob([vCardData], { type: 'text/vcard' });
    const url = window.URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = '贾建明_名片.vcf';
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    window.URL.revokeObjectURL(url);
    
    showNotification('名片已下载！', 'success');
}

// 分享资料功能
function shareProfile() {
    if (navigator.share) {
        navigator.share({
            title: '贾建明 - 跨境电商代运营专家',
            text: '九龙坡区质鼎汽车配件营业部 - 专业跨境电商代运营服务',
            url: window.location.href
        }).then(() => {
            showNotification('分享成功！', 'success');
        }).catch((error) => {
            console.log('分享失败:', error);
            fallbackShare();
        });
    } else {
        fallbackShare();
    }
}

// 备用分享方法
function fallbackShare() {
    const url = window.location.href;
    navigator.clipboard.writeText(url).then(() => {
        showNotification('链接已复制到剪贴板！', 'success');
    }).catch(() => {
        showNotification('请手动复制链接：' + url, 'info');
    });
}

// 联系表单处理
document.getElementById('contactForm').addEventListener('submit', function(e) {
    e.preventDefault();
    
    const formData = new FormData(this);
    const data = Object.fromEntries(formData);
    
    // 这里可以添加实际的表单提交逻辑
    console.log('表单数据:', data);
    
    // 模拟提交成功
    showNotification('咨询已发送，我们会尽快联系您！', 'success');
    this.reset();
});

// 显示通知
function showNotification(message, type = 'info') {
    const notification = document.createElement('div');
    notification.className = `notification ${type}`;
    notification.textContent = message;
    
    notification.style.cssText = `
        position: fixed;
        top: 90px;
        right: 20px;
        background: ${type === 'success' ? '#28a745' : type === 'error' ? '#dc3545' : '#17a2b8'};
        color: white;
        padding: 15px 20px;
        border-radius: 8px;
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
        z-index: 1001;
        font-size: 14px;
        font-weight: 500;
        transform: translateX(100%);
        transition: transform 0.3s ease;
        max-width: 300px;
    `;
    
    document.body.appendChild(notification);
    
    setTimeout(() => {
        notification.style.transform = 'translateX(0)';
    }, 100);
    
    setTimeout(() => {
        notification.style.transform = 'translateX(100%)';
        setTimeout(() => {
            if (document.body.contains(notification)) {
                document.body.removeChild(notification);
            }
        }, 300);
    }, 3000);
}

// 数字动画效果
function animateNumbers() {
    const stats = document.querySelectorAll('.stat-item h3');
    stats.forEach(stat => {
        const target = parseInt(stat.textContent);
        let current = 0;
        const increment = target / 50;
        const timer = setInterval(() => {
            current += increment;
            if (current >= target) {
                stat.textContent = target + (stat.textContent.includes('+') ? '+' : '');
                clearInterval(timer);
            } else {
                stat.textContent = Math.floor(current) + (stat.textContent.includes('+') ? '+' : '');
            }
        }, 40);
    });
}

// 当统计数字进入视口时开始动画
const statsObserver = new IntersectionObserver(function(entries) {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            animateNumbers();
            statsObserver.unobserve(entry.target);
        }
    });
}, { threshold: 0.5 });

const heroStats = document.querySelector('.hero-stats');
if (heroStats) {
    statsObserver.observe(heroStats);
}

// 服务卡片悬停效果
document.querySelectorAll('.service-card').forEach(card => {
    card.addEventListener('mouseenter', function() {
        this.style.transform = 'translateY(-10px)';
        this.style.boxShadow = '0 15px 40px rgba(0, 0, 0, 0.15)';
    });
    
    card.addEventListener('mouseleave', function() {
        this.style.transform = 'translateY(-5px)';
        this.style.boxShadow = '0 5px 20px rgba(0, 0, 0, 0.1)';
    });
});

// 平台项目悬停效果
document.querySelectorAll('.platform-item').forEach(item => {
    item.addEventListener('mouseenter', function() {
        this.style.transform = 'translateY(-10px)';
        this.style.boxShadow = '0 10px 30px rgba(0, 0, 0, 0.15)';
    });
    
    item.addEventListener('mouseleave', function() {
        this.style.transform = 'translateY(-5px)';
        this.style.boxShadow = '0 5px 20px rgba(0, 0, 0, 0.1)';
    });
});