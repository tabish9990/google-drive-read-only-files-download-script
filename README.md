# Google-drive-read-only-files-download-script
Download any read-only file from google drive with just few clicks. The script works likw magic, just past it and boom file starts to download.

 
## Instruction


* Open the PDF from Google Drive,
* Click Preview,
* Then on top right page click on three vertical dots menu -> Open in new window,
* Scroll down pdf to end of content and make sure all of content is loaded,
* Inspect element your browser,
* Copy and paste script below on console tab,



      (function () {
      console.log("Loading script ...");
  
      let script = document.createElement("script");
      script.onload = function () {
      const { jsPDF } = window.jspdf;

      
      let pdf = null;
      let imgElements = document.getElementsByTagName("img");
      let validImgs = [];
      let initPDF = true;

      console.log("Scanning content ...");
      for (let i = 0; i < imgElements.length; i++) {
      let img = imgElements[i];

      let checkURLString = "blob:https://drive.google.com/";
      if (img.src.substring(0, checkURLString.length) !== checkURLString) {
        continue;
      }

      //   if (!/^blob:/.test(img.src)) {
      //     continue;
      //   }

      validImgs.push(img);
      }

      console.log(`${validImgs.length} content found!`);
      console.log("Generating PDF file ...");
      for (let i = 0; i < validImgs.length; i++) {
      let img = validImgs[i];
      let canvasElement = document.createElement("canvas");
      let con = canvasElement.getContext("2d");
      canvasElement.width = img.naturalWidth;
      canvasElement.height = img.naturalHeight;
      con.drawImage(img, 0, 0, img.naturalWidth, img.naturalHeight);
      let imgData = canvasElement.toDataURL();

      let orientation;
      if (img.naturalWidth > img.naturalHeight) {
        //console.log("Landscape");
        orientation = "l";
        //ratio = img.naturalWidth/img.naturalHeight
      } else {
        //console.log("Portrait");
        orientation = "p";
        //ratio = img.naturalWidth/img.naturalHeight
      }

      let pageWidth = img.naturalWidth;
      let pageHeight = img.naturalHeight;

      if (initPDF) {
        pdf = new jsPDF({
          orientation: orientation,
          unit: "px",
          format: [pageWidth, pageHeight],
        });
        initPDF = false;
      }

      if (!initPDF) {
        pdf.addImage(imgData, "PNG", 0, 0, pageWidth, pageHeight, "", "SLOW");
        if (i !== validImgs.length - 1) {
          pdf.addPage();
        }
      }

      const percentages = Math.floor(((i + 1) / validImgs.length) * 100);
      console.log(`Processing content ${percentages}%`);
      }

      // check if title contains .pdf in end of the title
      let title = document.querySelector('meta[itemprop="name"]').content;
      if (title.split(".").pop() !== "pdf") {
      title = title + ".pdf";
      }

      // Download the generated PDF.
      console.log("Downloading PDF file ...");
      pdf.save(title, { returnPromise: true }).then(() => {
      document.body.removeChild(script);
      console.log("PDF downloaded!");
      });
      };

      let scriptURL = "https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js";
      let trustedURL;
      if (window.trustedTypes && trustedTypes.createPolicy) {
      const policy = trustedTypes.createPolicy("myPolicy", {
      createScriptURL: (input) => {
        return input;
      },
      });
      trustedURL = policy.createScriptURL(scriptURL);
      } else {
      trustedURL = scriptURL;
      }

      script.src = trustedURL;
      document.body.appendChild(script);
      })();


* Wait script processing and downloading pdf file,
* Fast or slow pdf processing based on the pdf content it self,
  Enjoyy....

## Sources

This script is updated version of https://github.com/zeltox/Google-Drive-PDF-Downloader
