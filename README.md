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
