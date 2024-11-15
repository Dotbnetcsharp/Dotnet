
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
