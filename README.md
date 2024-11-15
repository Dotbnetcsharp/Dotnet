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
