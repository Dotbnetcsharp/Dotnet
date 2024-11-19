var dd = {
  content: [
    { text: 'Tables', style: 'header' },
    {
      text: 'Official documentation is in progress, this document is just a glimpse of what is possible with pdfmake.',
    },
    {
      text: 'A simple table (no headers, no width specified, no spans, no styling)',
      style: 'subheader',
    },
    {
      style: 'tableExample',
      table: {
        body: [
          ['Column 1', 'Column 2', 'Column 3'],
          ['One value goes here', 'Another one here', 'OK?'],
        ],
      },
      layout: {
        dontBreakRows: true, // Ensures rows are not split across pages
      },
    },
    {
      text: 'Another table example with similar behavior',
      style: 'subheader',
    },
    {
      style: 'tableExample',
      table: {
        body: [
          ['Column 1', 'Column 2', 'Column 3'],
          ['One value goes here', 'Another one here', 'OK?'],
        ],
      },
      layout: {
        dontBreakRows: true, // Ensures rows are not split across pages
      },
    },
  ],
  styles: {
    header: {
      fontSize: 18,
      bold: true,
    },
    subheader: {
      fontSize: 15,
      bold: true,
    },
    tableExample: {
      margin: [0, 5, 0, 15],
    },
  },
  pageBreakBefore: function (currentNode, followingNodesOnPage, nodesOnNextPage) {
    // Move to the next page if the content cannot fit
    return currentNode.pageNumbers.length && currentNode.pageNumbers[0] !== currentNode.pageNumbers[currentNode.pageNumbers.length - 1];
  },
};
