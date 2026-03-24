<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>가족 안심 보험 안내문 (푸터 고정 레이아웃)</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    <!-- 라이브러리 추가: PDF, 이미지, PPT 저장용 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/gitbrent/pptxgenjs@3.12.0/dist/pptxgen.bundle.js"></script>
    <style>
        /* A4 세로 인쇄 설정 */
        @page {
            size: A4;
            margin: 0;
        }
        body {
            margin: 0;
            padding: 0;
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #f4f4f4;
            display: flex;
            justify-content: center;
            -webkit-print-color-adjust: exact;
        }

        /* 저장 버튼 플로팅 메뉴 */
        .export-menu {
            position: fixed;
            top: 20px;
            right: 20px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            z-index: 1000;
        }
        .export-btn {
            padding: 10px 16px;
            background-color: #3b5a7d;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 13px;
            font-weight: 500;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: all 0.2s;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .export-btn:hover {
            background-color: #2e4d68;
            transform: translateY(-2px);
        }
        .export-btn.pdf { background-color: #d32f2f; }
        .export-btn.img { background-color: #388e3c; }
        .export-btn.ppt { background-color: #f57c00; }

        /* A4 컨테이너 (Flex 설정으로 하단 고정 구현) */
        .a4-page {
            width: 210mm;
            height: 297mm;
            background-color: #ffffff;
            position: relative;
            overflow: hidden;
            box-shadow: 0 0 20px rgba(0,0,0,0.1);
            padding: 15mm 15mm 12mm 15mm;
            box-sizing: border-box;
            display: flex;
            flex-direction: column; 
            /* 내부 요소 간의 최소 간격 보장 */
        }

        /* 디자인 요소 */
        .top-bar {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 6mm;
            background: linear-gradient(90deg, #5b8c5a, #3b5a7d);
        }

        /* 헤더 섹션 */
        header {
            text-align: center;
            margin-top: 5mm;
            margin-bottom: 8mm;
            flex-shrink: 0;
        }
        .main-title {
            font-size: 2.6rem;
            color: #2e4d68;
            font-weight: 900;
            line-height: 1.3;
            letter-spacing: -1.5px;
            margin: 0;
            outline: none;
        }
        .sub-desc {
            font-size: 1.2rem;
            color: #666;
            margin-top: 12px;
            font-weight: 400;
            outline: none;
        }

        /* 메인 비주얼 영역 */
        .main-visual-area {
            width: 100%;
            height: 140mm;
            margin-bottom: 8mm;
            border: 2px dashed #ddd;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            overflow: hidden;
            background-color: #fafafa;
            position: relative;
            flex-shrink: 0; 
        }
        .main-visual-area:hover {
            background-color: #f0f0f0;
        }
        .main-visual-area img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
        }
        .upload-placeholder {
            text-align: center;
            color: #aaa;
            padding: 20px;
        }

        /* 중간 문구 영역 */
        .middle-slogan {
            text-align: center;
            margin-bottom: 5mm;
            flex-grow: 1; /* 남은 공간을 채워서 푸터를 아래로 밀어줌 */
            display: flex;
            align-items: center;
            justify-content: center;
        }

        /* 하단 푸터 고정 로직 */
        footer {
            flex-shrink: 0; /* 크기가 줄어들지 않도록 고정 */
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            border-top: 2px solid #3b5a7d;
            padding-top: 6mm;
            padding-bottom: 2mm;
            background-color: white; /* 렌더링 시 투명도 방지 */
        }

        .profile-section {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        /* QR코드 영역 */
        .qr-area {
            width: 25mm;
            height: 25mm;
            border: 1px dashed #ccc;
            border-radius: 4px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            overflow: hidden;
            background: #fff;
            flex-shrink: 0;
        }
        .qr-area img {
            width: 100%;
            height: 100%;
            object-fit: contain;
        }

        .info-text {
            display: flex;
            flex-direction: column;
        }
        .info-role { font-size: 1rem; color: #777; margin-bottom: 2px; }
        .info-name { font-size: 1.5rem; font-weight: 700; color: #2e4d68; }
        .info-phone { font-size: 1.8rem; font-weight: 900; color: #222; margin-top: 2px; white-space: nowrap; }

        .legal-copy {
            text-align: right;
            font-size: 0.75rem;
            color: #999;
            line-height: 1.5;
            max-width: 90mm;
        }

        /* 편집 가능한 스타일 */
        .editable {
            outline: none;
            transition: background 0.2s;
            border-radius: 4px;
        }
        .editable:hover { background: rgba(0,0,0,0.03); }

        .tip-box {
            position: fixed;
            left: 20px;
            bottom: 20px;
            background: #3b5a7d;
            color: white;
            padding: 12px 20px;
            border-radius: 30px;
            font-size: 13px;
            z-index: 999;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        @media print {
            .tip-box, .export-menu, input[type="file"] { display: none; }
            body { background: white; }
            .a4-page { box-shadow: none; margin: 0; }
        }
    </style>
</head>
<body>

<div class="export-menu">
    <button class="export-btn pdf" onclick="exportToPDF()">📑 PDF 저장</button>
    <button class="export-btn img" onclick="exportToImage()">🖼️ 이미지 저장</button>
    <button class="export-btn ppt" onclick="exportToPPT()">📊 PPT 저장</button>
</div>

<div class="tip-box">✨ 이미지 첨부 후 PDF 저장 시 레이아웃이 고정되도록 개선되었습니다.</div>

<div id="capture-area" class="a4-page">
    <div class="top-bar"></div>
    
    <header>
        <h1 class="main-title editable" contenteditable="true">오늘 우리 가족의 행복,<br>내일도 안전한가요?</h1>
        <p class="sub-desc editable" contenteditable="true">나와 가족의 건강을 지키는 생명보험부터 생활의 리스크를 방어하는 손해보험까지!</p>
    </header>

    <input type="file" id="mainImageInput" accept="image/*" style="display:none">
    <div class="main-visual-area" onclick="document.getElementById('mainImageInput').click()">
        <div id="mainImagePreview" class="upload-placeholder">
            <span style="font-size: 60px;">🖼️</span><br>
            <p style="font-size: 1.2rem; margin-top: 15px; color: #888;">클릭하여 메인 이미지를 업로드하세요</p>
        </div>
    </div>

    <div class="middle-slogan">
        <span class="editable" contenteditable="true" style="color: #5b8c5a; font-weight: 700; font-size: 1.25rem; border-bottom: 2px solid #5b8c5a; padding-bottom: 5px; display: inline-block;">
            보장 분석부터 맞춤 설계까지, 전문가와 상의하세요!
        </span>
    </div>

    <!-- 하단에 고정된 푸터 -->
    <footer>
        <div class="profile-section">
            <input type="file" id="qrInput" accept="image/*" style="display:none">
            <div class="qr-area" onclick="document.getElementById('qrInput').click()">
                <div id="qrPreview" style="display: flex; flex-direction: column; align-items: center;">
                    <span style="font-size: 20px; margin-bottom: 3px;">📷</span>
                    <span style="font-size: 8px; color:#aaa; text-align:center">QR 업로드</span>
                </div>
            </div>
            
            <div class="info-text">
                <span class="info-role editable" contenteditable="true">가족 안심 보험 전문 상담사</span>
                <span class="info-name editable" contenteditable="true">홍길동 팀장</span>
                <span class="info-phone editable" contenteditable="true">010-1234-5678</span>
            </div>
        </div>

        <div class="legal-copy editable" contenteditable="true">
            본 홍보물은 고객의 이해를 돕기 위해 제작되었습니다.<br>
            보험계약 체결 전 상품설명서 및 약관을 반드시 읽어보시기 바랍니다.<br>
            자세한 내용은 전문 상담사를 통해 확인하실 수 있습니다.
        </div>
    </footer>
</div>

<script>
    // 이미지 업로드 핸들러
    function handleImageUpload(inputId, previewId, containerClass) {
        document.getElementById(inputId).addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(event) {
                    const preview = document.getElementById(previewId);
                    const fitType = inputId === 'qrInput' ? 'contain' : 'cover';
                    preview.innerHTML = `<img src="${event.target.result}" alt="Uploaded Image" style="width:100%; height:100%; object-fit:${fitType}; display:block;">`;
                    if (containerClass) {
                        const container = document.querySelector(containerClass);
                        container.style.border = 'none';
                    }
                };
                reader.readAsDataURL(file);
            }
        });
    }

    handleImageUpload('mainImageInput', 'mainImagePreview', '.main-visual-area');
    handleImageUpload('qrInput', 'qrPreview', '.qr-area');

    // 모든 이미지가 로드되었는지 확인하는 헬퍼 함수
    function waitImages(area) {
        const imgs = area.querySelectorAll('img');
        const promises = Array.from(imgs).map(img => {
            if (img.complete) return Promise.resolve();
            return new Promise(resolve => {
                img.onload = resolve;
                img.onerror = resolve;
            });
        });
        return Promise.all(promises);
    }

    // 이미지 저장
    async function exportToImage() {
        const area = document.getElementById('capture-area');
        await waitImages(area);
        const canvas = await html2canvas(area, { 
            scale: 2, 
            useCORS: true, 
            backgroundColor: "#ffffff",
            logging: false
        });
        const link = document.createElement('a');
        link.download = '보험_안내문.png';
        link.href = canvas.toDataURL('image/png');
        link.click();
    }

    // PDF 저장 (푸터 위치 고정 및 렌더링 이슈 해결)
    async function exportToPDF() {
        const { jsPDF } = window.jspdf;
        const area = document.getElementById('capture-area');
        
        // 1. 모든 이미지가 브라우저에 완전히 로드될 때까지 대기
        await waitImages(area);
        
        // 2. html2canvas 옵션 강화 (y축 오프셋 및 윈도우 크기 고정)
        const canvas = await html2canvas(area, { 
            scale: 2, 
            useCORS: true, 
            backgroundColor: "#ffffff",
            scrollY: -window.scrollY, // 현재 스크롤 위치 무시
            windowWidth: 210 * 3.7795, // A4 너비를 픽셀로 환산 (정적 뷰포트 강제)
            windowHeight: 297 * 3.7795, // A4 높이를 픽셀로 환산
            onclone: (clonedDoc) => {
                // 복제된 문서에서 푸터가 확실히 바닥에 붙어 있도록 강제
                const clonedPage = clonedDoc.getElementById('capture-area');
                clonedPage.style.height = '297mm';
                clonedPage.style.display = 'flex';
                clonedPage.style.flexDirection = 'column';
            }
        });
        
        const imgData = canvas.toDataURL('image/png');
        const pdf = new jsPDF('p', 'mm', 'a4');
        pdf.addImage(imgData, 'PNG', 0, 0, 210, 297, undefined, 'FAST');
        pdf.save('보험_안내문.pdf');
    }

    // PPT 저장
    async function exportToPPT() {
        const area = document.getElementById('capture-area');
        await waitImages(area);
        const canvas = await html2canvas(area, { scale: 2, useCORS: true, backgroundColor: "#ffffff" });
        const imgData = canvas.toDataURL('image/png');
        let pptx = new PptxGenJS();
        let slide = pptx.addSlide();
        slide.addImage({ data: imgData, x: 0, y: 0, w: '100%', h: '100%' });
        pptx.writeFile({ fileName: '보험_안내문.pptx' });
    }
</script>

</body>
</html>
