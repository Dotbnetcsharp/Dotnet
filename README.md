function applyDynamicPageBreak(contentArray) {
  const modifiedContent = [];
  let currentPageHeight = 0;
  const maxPageHeight = 750; // Adjust this based on your PDF's page size and margins.

  contentArray.forEach((node) => {
    // Measure the height of the current node. Assume each line is ~20 units high.
    const nodeHeight = estimateNodeHeight(node);

    // Check if adding this node exceeds the page height
    if (currentPageHeight + nodeHeight > maxPageHeight) {
      // Add a page break before the node
      modifiedContent.push({ text: '', pageBreak: 'after' });
      currentPageHeight = 0; // Reset height for the new page
    }

    // Add the node to the modified content
    modifiedContent.push(node);

    // Update the current page height
    currentPageHeight += nodeHeight;
  });

  return modifiedContent;
}

// Example function to estimate node height (customize based on your data)
function estimateNodeHeight(node) {
  if (node.text) {
    const lines = node.text.split('\n').length;
    return lines * 20; // Assume 20 units height per line
  }
  return 30; // Default height for nodes without text
}
const document = {
  pageMargins: [40, 165, 60, 50],
  content: applyDynamicPageBreak(apiResponseContent), // Process content dynamically
  styles: {
    header: { fontSize: 19, bold: true, font: 'Calibri' },
    normal: { fontSize: 12, font: 'Calibri' }
  }
};













var fonts = {
  Calibri: {
    normal: './fonts/Calibri/calibri.ttf',
    bold: './fonts/Calibri/calibrib.ttf',
    italics: './fonts/Calibri/calibril.ttf',
  },
  CenturyGothic: {
    normal: './fonts/CenturyGothic/CenturyGothic.ttf',
    bold: './fonts/CenturyGothic/CenturyGothicB.ttf',
  },
};

var printer = new PdfPrinter(fonts);
log.info('Starting to create PDF from JSON');

// To store in local file
const fullFile = 'C:/NTP';
let pdfPath = `${fullFile}/ntpreports${uuidV4()}.pdf`;

let progress = 0;





try {
  // Step 1: Add custom page break logic
  const processResultsForPageBreak = (data) => {
    const processedContent = [];
    let remainingPageHeight = 800; // Example page height
    const pageMargin = 50; // Bottom margin for each page

    data.forEach((result) => {
      const nodeHeight = estimateNodeHeight(result);

      if (remainingPageHeight - nodeHeight < pageMargin) {
        processedContent.push({ text: '', pageBreak: 'after' }); // Add page break
        remainingPageHeight = 800; // Reset for new page
      }

      processedContent.push(result);
      remainingPageHeight -= nodeHeight;
    });

    return processedContent;
  };

  const estimateNodeHeight = (node) => {
    // Example: Estimate height of a node
    return node.text.length * 5; // Adjust multiplier based on your content
  };

  // Example JSON data
  const jsonData = [
    { text: 'Parameter 1: Some data...' },
    { text: 'Parameter 2: More data...' },
    // Add more items here as per your input
  ];

  // Preprocess the content to handle page breaks
  const processedContent = processResultsForPageBreak(jsonData);

  // Step 2: Define the PDF document with the processed content
  const document = {
    pageMargins: [40, 60, 40, 60],
    content: processedContent,
    styles: {
      header: { fontSize: 16, bold: true },
      normal: { fontSize: 12 },
    },
  };

  // Step 3: Generate the PDF with a progress callback
  var pdfDoc = printer.createPdfKitDocument(document, {
    bufferPages: true,
    progressCallback: (d) => {
      if (d > progress) {
        progress = d + 1;
        console.log(`Progress: ${progress}%`);
      }
    },
  });

  // Step 4: Ensure the directory exists and save the file
  if (!fs.existsSync(fullFile)) {
    fs.mkdirSync(fullFile, { recursive: true });
  }

  createTheFile(pdfDoc, pdfPath); // Use your existing function to handle file writing
} catch (ex) {
  console.error('Error generating PDF:', ex);
}

return { pdfPath, pdfDoc };

// Function to upload the PDF to Azure Blob Storage
const writeBlob = async (filePath, container, fileName, extension) => {
  return new Promise((resolve) => {
    var readStream = fs.createReadStream(filePath);
    fileName = filePath.split('\\').pop();
    let jMessage = fileName.substring(0, fileName.lastIndexOf('.')) || fileName;

    const blobService = BlobServiceClient.fromConnectionString(process.env.AZURE_STORAGE_CONNECTION_STRING);
    const blobContainer = blobService.getContainerClient(container);
    const blobClient = blobContainer.getBlockBlobClient(jMessage + extension);

    var stats = fs.statSync(filePath);
    var fileSizeInBytes = stats['size'];

    readStream.on('open', async function () {
      log.info('Read stream opened');
      try {
        await blobClient.uploadStream(readStream, fileSizeInBytes);
        log.info('Created report PDF in blob', fileName);
        resolve(blobClient.url);
      } catch (error) {
        console.error('Error uploading to blob:', error);
        resolve();
      }
    });
  });
};

// Function to handle writing the PDF file
function createTheFile(pdfDoc, fullFilePath) {
  var log = log4js.getLogger();
  log.level = 'debug';

  return new Promise((resolve) => {
    try {
      fsExtra.ensureFileSync(fullFilePath);

      let stream = fs.createWriteStream(fullFilePath);
      stream.on('open', function () {
        pdfDoc.pipe(stream);
        pdfDoc.end();
      });

      stream.on('error', (err) => {
        console.error('Error writing stream:', err);
      });

      stream.on('finish', () => {
        log.info('PDF generation finished.');
        resolve();
      });

      stream.on('close', () => {
        log.info('Stream closed.');
        resolve();
      });
    } catch (e) {
      log.error('Error:', e);
    }
  });
}















let progress = 0;

try {
  // Step 1: Pre-process JSON data for custom page breaks
  const processResultsForPageBreak = (data) => {
    const processedContent = [];
    let remainingPageHeight = 800; // Example: Define your page height
    const pageMargin = 50; // Define bottom margin

    data.forEach((result) => {
      const nodeHeight = estimateNodeHeight(result);

      if (remainingPageHeight - nodeHeight < pageMargin) {
        processedContent.push({ text: '', pageBreak: 'after' }); // Add page break
        remainingPageHeight = 800; // Reset for new page
      }

      processedContent.push(result);
      remainingPageHeight -= nodeHeight;
    });

    return processedContent;
  };

  const estimateNodeHeight = (node) => {
    return node.text.length * 5; // Example: Approximation of node height
  };

  // Replace with your actual JSON input
  const jsonData = [
    { text: 'Parameter 1: Some data...' },
    { text: 'Parameter 2: More data...' },
    // Add more items as per your JSON structure
  ];

  // Pre-process content
  const processedContent = processResultsForPageBreak(jsonData);

  // Step 2: Define the PDF document
  const document = {
    pageMargins: [40, 60, 40, 60],
    content: processedContent,
    styles: {
      header: { fontSize: 16, bold: true },
      normal: { fontSize: 12 },
    },
  };

  // Step 3: Generate the PDF with progress callback
  var pdfDoc = printer.createPdfKitDocument(document, {
    bufferPages: true, // Buffer pages to improve performance
    progressCallback: (d) => {
      if (d > progress) {
        progress = d + 1; // Update progress
        console.log(`Progress: ${progress}%`);
      }
    },
  });

  // Step 4: Write PDF to file
  const fullFile = './path/to/output.pdf'; // Update this with your file path
  if (!fs.existsSync(fullFile)) {
    fs.mkdirSync(fullFile, { recursive: true }); // Ensure the directory exists
  }

  pdfDoc.pipe(fs.createWriteStream(fullFile)); // Write to the output file
  pdfDoc.end(); // Finalize the PDF
} catch (ex) {
  console.error('Error generating PDF:', ex);
}






document.content.push({
    table: {
        headerRows: 1,
        widths: ['3%', '2%', '11%', '12%', '19%', '19%', '23%', '14%', '3%'],
        body: [
            [
                { text: 'RUN', fontSize: 8, bold: true, alignment: 'center' },
                { text: '#', fontSize: 8, bold: true, alignment: 'center' },
                { text: 'TYPE', fontSize: 8, bold: true, alignment: 'center' },
                { text: 'INST, BK/PG', fontSize: 8, bold: true, alignment: 'center' },
                { text: 'APN', fontSize: 8, bold: true, alignment: 'center' },
                { text: 'ADDRESS', fontSize: 8, bold: true, alignment: 'center' },
                { text: 'ASSESSED OWNER/GI NAME', fontSize: 8, bold: true, alignment: 'center' },
                { text: 'PROP ID MATCH', fontSize: 8, bold: true, alignment: 'center' },
                { text: 'CT', fontSize: 8, bold: true, alignment: 'center' }
            ],
            // Add your data rows here in the same format as above
        ]
    },
    layout: {
        hLineWidth: function(i, node) {
            return (i === 0 || i === node.table.body.length) ? 1 : 1;
        },
        vLineWidth: function(i, node) {
            return (i === 0 || i === node.table.widths.length) ? 1 : 1;
        },
        hLineColor: function(i, node) {
            return (i === 0 || i === node.table.body.length) ? 'gray' : 'gray';
        },
        vLineColor: function(i, node) {
            return (i === 0 || i === node.table.widths.length) ? 'gray' : 'gray';
        }
    },
    lineHeight: 1.5,  // Adjust this value to control spacing between rows
    font: 'Calibri',
    style: "table"
});



document.content[document.content.length - 1].table.body.push(
    [
        { text: s.Run, nowrap: true, alignment: 'center', bold: true, border: [0, 0, 0, 0], borderColor: '#F5F5F5' },
        { text: cnt, nowrap: true, alignment: 'center', border: [0, 0, 0, 0], borderColor: '#F5F5F5' },
        { text: bool ? 'PROPERTY' : doc ? 'Document' : 'NAME', bold: true, border: [0, 0, 0, 0], borderColor: '#F5F5F5' },
        { text: doc ? val : '', border: [0, 0, 0, 0], borderColor: '#F5F5F5' },
        { text: s.APN, border: [0, 0, 0, 0], borderColor: '#F5F5F5', fillColor: '#F5F5F5' },
        { text: st_flag ? add + s.Address : '', border: [0, 0, 0, 0], borderColor: '#F5F5F5' },
        { text: bool ? s.OwnerName : doc ? ':name' : '', border: [0, 0, 0, 0], borderColor: '#F5F5F5' },
        { text: s.PropertyId, bold: true, border: [true, 0, 0, 0], fillColor: '#F5F5F5' },
        { text: CT1, alignment: 'center', bold: true, nowrap: true, border: [0, 0, 0, 0] }
    ]
);

// Add an empty row with a specific height to create space
document.content[document.content.length - 1].table.body.push(
    [
        { text: '', colSpan: 9, border: [0, 0, 0, 0], margin: [0, 5, 0, 5] }
    ]
);
