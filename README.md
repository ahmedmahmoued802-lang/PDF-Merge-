<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PDF Merge</title>
    <!-- استدعاء مكتبات PDF-lib و PDF.js لعرض ومعالجة ملفات PDF -->
    <script src="https://unpkg.com/pdf-lib@1.4.0/dist/pdf-lib.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
    <script>
        pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js';
    </script>
    <style>
        :root {
            --primary-color: #6366f1;
            --danger-color: #ef4444;
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --text-color: #f8fafc;
            --border-color: #334155;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 900px;
            margin: 0 auto;
            background: var(--card-bg);
            padding: 30px;
            border-radius: 16px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.5);
            text-align: center;
            border: 1px solid var(--border-color);
        }
        h1 {
            color: #818cf8;
            margin-bottom: 5px;
            font-size: 2.2rem;
        }
        .author {
            color: #94a3b8;
            font-size: 1rem;
            margin-bottom: 25px;
            font-weight: 500;
        }
        .upload-section {
            border: 2px dashed var(--border-color);
            padding: 25px;
            text-align: center;
            border-radius: 12px;
            margin-bottom: 20px;
            cursor: pointer;
            transition: border-color 0.3s, background 0.3s;
            background: rgba(15, 23, 42, 0.5);
        }
        .upload-section:hover {
            border-color: var(--primary-color);
            background: rgba(99, 102, 241, 0.05);
        }
        input[type="file"] {
            display: none;
        }
        .btn-upload {
            background-color: var(--primary-color);
            color: white;
            padding: 10px 20px;
            border-radius: 8px;
            display: inline-block;
            cursor: pointer;
            font-weight: bold;
            margin-top: 10px;
        }
        .files-list {
            list-style: none;
            padding: 0;
            margin-bottom: 20px;
            text-align: right;
        }
        .file-item {
            background: #0f172a;
            padding: 15px;
            margin-bottom: 12px;
            border-radius: 10px;
            border: 1px solid var(--border-color);
        }
        .file-main-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .file-info {
            display: flex;
            align-items: center;
            gap: 15px;
        }
        .file-thumb {
            width: 45px;
            height: 45px;
            object-fit: cover;
            border-radius: 6px;
            border: 1px solid var(--border-color);
        }
        .btn-delete {
            background-color: var(--danger-color);
            color: white;
            border: none;
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
        }
        .btn-view {
            background-color: #3b82f6;
            color: white;
            border: none;
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
            margin-left: 8px;
        }
        /* قسم معاينة صفحات الـ PDF */
        .pdf-preview-container {
            display: none;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px dashed var(--border-color);
            text-align: right;
        }
        .pages-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 10px;
            max-height: 250px;
            overflow-y: auto;
            padding: 5px;
        }
        .page-card {
            background: #1e293b;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 5px;
            text-align: center;
            width: 90px;
        }
        .page-thumb {
            width: 80px;
            height: 100px;
            object-fit: contain;
            background: #000;
            border-radius: 4px;
        }
        .page-card button {
            background: var(--danger-color);
            color: white;
            border: none;
            padding: 3px 6px;
            font-size: 11px;
            border-radius: 4px;
            cursor: pointer;
            margin-top: 4px;
            width: 100%;
        }
        .controls {
            display: flex;
            flex-direction: column;
            gap: 15px;
            border-top: 1px solid var(--border-color);
            padding-top: 20px;
            text-align: right;
        }
        label {
            color: #cbd5e1;
            font-weight: 500;
        }
        input[type="text"] {
            padding: 12px;
            background: #0f172a;
            border: 1px solid var(--border-color);
            color: white;
            border-radius: 8px;
            font-size: 16px;
            outline: none;
            direction: ltr;
            text-align: left;
        }
        input[type="text"]:focus {
            border-color: var(--primary-color);
        }
        .btn-save {
            background-color: #10b981;
            color: white;
            border: none;
            padding: 14px;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s;
        }
        .btn-save:hover {
            background-color: #059669;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>PDF Merge</h1>
    <div class="author">By:Ahmed Mahmoud Fathy</div>

    <div class="upload-section" onclick="document.getElementById('fileInput').click()">
        <p>اضغط هنا لاختيار ملفات PDF أو الصور من جهازك</p>
        <span class="btn-upload">اختر الملفات</span>
        <input type="file" id="fileInput" multiple>
    </div>

    <ul id="filesList" class="files-list"></ul>

    <div class="controls">
        <label for="outputFileName">اسم الملف الناتج:</label>
        <input type="text" id="outputFileName" placeholder="اكتب اسم الملف (بدون امتداد)..." value="merged_document">
        <button class="btn-save" onclick="generatePDF()">دمج وحفظ الملف</button>
    </div>
</div>

<script>
    // سنقوم بتخزين كائنات الملفات بالإضافة إلى حالة الصفحات المحذوفة لكل ملف PDF
    let uploadedFiles = [];

    document.getElementById('fileInput').addEventListener('change', async function(e) {
        const files = Array.from(e.target.files);
        for (const file of files) {
            let fileObj = {
                file: file,
                name: file.name,
                type: file.type,
                arrayBuffer: await file.arrayBuffer(),
                excludedPages: [] // لتخزين أرقام الصفحات المحذوفة في حالة كان الملف PDF
            };
            uploadedFiles.push(fileObj);
        }
        renderFilesList();
        e.target.value = '';
    });

    async function renderFilesList() {
        const listContainer = document.getElementById('filesList');
        listContainer.innerHTML = '';

        if (uploadedFiles.length === 0) {
            listContainer.innerHTML = '<p style="text-align: center; color: #64748b;">لم يتم اختيار أي ملفات بعد.</p>';
            return;
        }

        for (let index = 0; index < uploadedFiles.length; index++) {
            const item = uploadedFiles[index];
            const li = document.createElement('li');
            li.className = 'file-item';

            let thumbHTML = '';
            const isImage = item.type.startsWith('image/') || /\.(jpg|jpeg|png|webp|bmp|gif)$/i.test(item.name);
            const isPdf = item.type === 'application/pdf' || /\.pdf$/i.test(item.name);

            if (isImage) {
                const url = URL.createObjectURL(item.file);
                thumbHTML = `<img src="${url}" class="file-thumb" alt="thumb">`;
            } else {
                thumbHTML = `<div style="width:45px; height:45px; background:#334155; display:flex; align-items:center; justify-content:center; border-radius:6px; font-weight:bold; font-size:12px; color:#cbd5e1;">PDF</div>`;
            }

            let viewBtnHTML = isPdf ? `<button class="btn-view" onclick="togglePdfPreview(${index})">رؤية وحذف صفحات</button>` : '';

            li.innerHTML = `
                <div class="file-main-row">
                    <div class="file-info">
                        ${thumbHTML}
                        <span>${item.name}</span>
                    </div>
                    <div>
                        ${viewBtnHTML}
                        <button class="btn-delete" onclick="removeFile(${index})">حذف الملف</button>
                    </div>
                </div>
                <div id="preview-${index}" class="pdf-preview-container">
                    <p style="font-size:13px; color:#94a3b8; margin:0 0 5px 0;">اضغط على "حذف" أسفل أي صفحة لاستبعادها:</p>
                    <div class="pages-grid" id="grid-${index}">جاري تحميل صفحات الـ PDF...</div>
                </div>
            `;
            listContainer.appendChild(li);
        }
    }

    function removeFile(index) {
        uploadedFiles.splice(index, 1);
        renderFilesList();
    }

    // عرض أو إخفاء صفحة معاينة صفحات الـ PDF
    async function togglePdfPreview(index) {
        const previewContainer = document.getElementById(`preview-${index}`);
        if (previewContainer.style.display === 'block') {
            previewContainer.style.display = 'none';
            return;
        }
        previewContainer.style.display = 'block';

        const gridContainer = document.getElementById(`grid-${index}`);
        if (gridContainer.dataset.loaded === "true") return; // تم التحميل مسبقاً

        try {
            const item = uploadedFiles[index];
            const loadingTask = pdfjsLib.getDocument({ data: item.arrayBuffer.slice(0) });
            const pdfDoc = await loadingTask.promise;
            gridContainer.innerHTML = '';

            for (let i = 1; i <= pdfDoc.numPages; i++) {
                const page = await pdfDoc.getPage(i);
                const viewport = page.getViewport({ scale: 0.3 }); // حجم مصغر للمعرض
                
                const canvas = document.createElement('canvas');
                const context = canvas.getContext('2d');
                canvas.height = viewport.height;
                canvas.width = viewport.width;

                await page.render({ canvasContext: context, viewport: viewport }).promise;

                const pageCard = document.createElement('div');
                pageCard.className = 'page-card';
                pageCard.id = `page-card-${index}-${i}`;

                if (item.excludedPages.includes(i - 1)) {
                    pageCard.style.opacity = '0.3';
                }

                pageCard.innerHTML = `
                    <img src="${canvas.toDataURL()}" class="page-thumb" alt="Page ${i}">
                    <div style="font-size:11px; margin-top:2px;">صيغة ${i}</div>
                    <button onclick="togglePageExclusion(${index}, ${i - 1}, this)">${item.excludedPages.includes(i - 1) ? 'استعادة' : 'حذف الصفحة'}</button>
                `;
                gridContainer.appendChild(pageCard);
            }
            gridContainer.dataset.loaded = "true";
        } catch (err) {
            console.error(err);
            gridContainer.innerHTML = '<span style="color:var(--danger-color);">عذراً، تعذر قراءة صفحات هذا الملف.</span>';
        }
    }

    // استبعاد أو استعادة صفحة معينة من الـ PDF
    function togglePageExclusion(fileIndex, pageIndexZeroBased, btnElement) {
        const item = uploadedFiles[fileIndex];
        const pageCard = document.getElementById(`page-card-${fileIndex}-${pageIndexZeroBased + 1}`);
        
        const pos = item.excludedPages.indexOf(pageIndexZeroBased);
        if (pos > -1) {
            item.excludedPages.splice(pos, 1);
            pageCard.style.opacity = '1';
            btnElement.textContent = 'حذف الصفحة';
        } else {
            item.excludedPages.push(pageIndexZeroBased);
            pageCard.style.opacity = '0.3';
            btnElement.textContent = 'استعادة';
        }
    }

    // عملية الدمج والتصدير النهائية
    async function generatePDF() {
        if (uploadedFiles.length === 0) {
            alert('الرجاء اختيار ملف واحد على الأقل للدمج.');
            return;
        }

        const { PDFDocument } = PDFLib;
        const mergedPdf = await PDFDocument.create();

        try {
            for (const item of uploadedFiles) {
                const isImage = item.type.startsWith('image/') || /\.(jpg|jpeg|png|webp|bmp|gif)$/i.test(item.name);
                const isPdf = item.type === 'application/pdf' || /\.pdf$/i.test(item.name);

                if (isPdf) {
                    const pdf = await PDFDocument.load(item.arrayBuffer);
                    const allIndices = pdf.getPageIndices();
                    // تصفية الصفحات واستبعاد المحذوفة منها
                    const targetIndices = allIndices.filter(idx => !item.excludedPages.includes(idx));
                    
                    if (targetIndices.length > 0) {
                        const copiedPages = await mergedPdf.copyPages(pdf, targetIndices);
                        copiedPages.forEach((page) => mergedPdf.addPage(page));
                    }
                } else if (isImage) {
                    let image;
                    if (item.type === 'image/png' || /\.png$/i.test(item.name)) {
                        image = await mergedPdf.embedPng(item.arrayBuffer);
                    } else {
                        image = await mergedPdf.embedJpg(item.arrayBuffer);
                    }

                    const page = mergedPdf.addPage([image.width, image.height]);
                    page.drawImage(image, {
                        x: 0,
                        y: 0,
                        width: image.width,
                        height: image.height,
                    });
                }
            }

            const pdfBytes = await mergedPdf.save();
            
            let fileNameInput = document.getElementById('outputFileName').value.trim();
            if (!fileNameInput) fileNameInput = "document";
            const finalName = fileNameInput.endsWith('.pdf') ? fileNameInput : fileNameInput + '.pdf';

            const blob = new Blob([pdfBytes], { type: 'application/pdf' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = finalName;
            link.click();

        } catch (error) {
            console.error(error);
            alert('حدث خطأ أثناء معالجة الملفات.');
        }
    }

    renderFilesList();
</script>

</body>
</html>
