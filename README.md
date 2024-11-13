{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch API Server",
      "skipFiles": ["<node_internals>/**"],
      "program": "${workspaceFolder}/server/app.js", // or use "${workspaceFolder}/server/api-server.js"
      "cwd": "${workspaceFolder}",
      "outFiles": ["${workspaceFolder}/compiled/**/*.js"], // Update this if needed based on where Babel outputs compiled files
      "sourceMaps": true, // Useful if you're using TypeScript or Babel
      "runtimeArgs": ["--nolazy", "--inspect-brk=9229"],
      "env": {
        "NODE_ENV": "development"
      },
      "console": "integratedTerminal"
    }
  ]
}
