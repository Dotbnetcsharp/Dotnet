const express = require('express');
const swaggerJsDoc = require('swagger-jsdoc');
const swaggerUi = require('swagger-ui-express');

const app = express();

// Swagger options
const swaggerOptions = {
  swaggerDefinition: {
    openapi: "3.0.0",
    info: {
      title: "Your API",
      version: "1.0.0",
      description: "API Information"
    }
  },
  apis: ["./routes/*.js"],  // Specify the path where your route files are located
};

// Initialize Swagger documentation
const swaggerDocs = swaggerJsDoc(swaggerOptions);

// Set up Swagger UI
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocs));

// Your other routes go here...

// Start your server
app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
