<template>
  <div id="app">
    <h1>Markdown to PDF</h1>
    <!-- Markdown input area -->
    <textarea v-model="markdown" placeholder="Markdown input here"></textarea>
    
    <!-- HTML preview -->
    <div class="preview" ref="preview" v-html="compiledMarkdown"></div>
    
    <!-- PDF download button -->
    <button @click="downloadPDF">Download PDF</button>

    <!-- Generate PDF candidate image preview -->
    <button @click="generatePdfPreview">PDF image preview</button>
    <!-- PDF image preview display -->
    <div v-if="pdfImageDataUrl">
      <h2>PDF Image preview (Margins and size will not be the same as the PDF.)</h2>
      <img :src="pdfImageDataUrl" alt="PDF Image preview" style="max-width: 100%;" />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, nextTick } from 'vue'
import { marked } from 'marked'
import html2pdf from 'html2pdf.js'
import html2canvas from 'html2canvas'

export default defineComponent({
  name: 'App',
  setup() {
    const markdown = ref<string>(`# Sample title

This is a sample of **Markdown**

## Table sample

| Header 1 | Header 2 |
| ------- | ------- |
| Cell 1   | Cell 2 |
| Cell 3   | Cell 4 |
`)
    const preview = ref<HTMLElement | null>(null)
    const pdfImageDataUrl = ref<string | null>(null)

    // Convert Markdown to HTML (marked.js)
    const compiledMarkdown = computed(() => marked(markdown.value))

    // Temporarily change the text color to black (#000) when generating PDF
    const applyPdfCaptureStyle = () => {
      if (preview.value) {
        preview.value.classList.add('pdf-capture')
      }
    }
    const removePdfCaptureStyle = () => {
      if (preview.value) {
        preview.value.classList.remove('pdf-capture')
      }
    }

    // PDF download processing (html2pdf.js)
    const downloadPDF = async () => {
      await nextTick();
      if (preview.value) {
        const originalBorder = preview.value.style.border;
        preview.value.style.border = "none";
        applyPdfCaptureStyle();

        const opt = {
          margin: 8,
          filename: 'document.pdf',
          image: { type: 'jpeg', quality: 0.98 },
          html2canvas: {
            scale: 2,
            useCORS: true,
            backgroundColor: '#ffffff'
          },
          jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' },
          pagebreak: { mode: ['avoid-all', 'css', 'legacy'] }
        };

        await html2pdf().set(opt).from(preview.value).save();
        removePdfCaptureStyle();
        preview.value.style.border = originalBorder;
      }
    }

    const generatePdfPreview = async () => {
      await nextTick();
      if (preview.value) {
        const originalBorder = preview.value.style.border;
        preview.value.style.border = "none";
        applyPdfCaptureStyle();
        const canvas = await html2canvas(preview.value, {
          scale: 2,
          useCORS: true,
          backgroundColor: '#ffffff'
        });
        pdfImageDataUrl.value = canvas.toDataURL('image/jpeg', 0.98);
        removePdfCaptureStyle();
        preview.value.style.border = originalBorder;
      }
    }

    return {
      markdown,
      compiledMarkdown,
      downloadPDF,
      generatePdfPreview,
      preview,
      pdfImageDataUrl
    }
  }
})
</script>

<style scoped>
/* Markdown */
textarea {
  width: 100%;
  height: 200px;
  padding: 10px;
  font-size: 16px;
  box-sizing: border-box;
}

/* preview */
.preview {
  border: none;
  padding: 20px;
  margin-top: 20px;
  max-width: 100%;
  background-color: #242424;
  color: #fff;
  text-align: left;
}

/* Temporarily apply class for PDF generation: Text color is black */
.pdf-capture {
  color: #000 !important;
  background-color: #ffffff !important;
  margin: 20px;
}

/* Specify page break avoidance for block elements to avoid page breaks */
.preview p,
.preview h1,
.preview h2,
.preview h3,
.preview table {
  page-break-inside: avoid;
}

/* Styling for tables in Markdown */
table {
  width: 100%;
  border-collapse: collapse;
}
table, th, td {
  border: 1px solid #5a5a5a;
}
th, td {
  padding: 8px;
  text-align: left;
}
th {
  background-color: #c8c8c8;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  margin-top: 20px;
  margin-right: 10px;
  cursor: pointer;
}
</style>