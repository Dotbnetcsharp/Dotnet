import "react-toastify/dist/ReactToastify.css";
import { Toaster } from 'react-hot-toast';

<App />
     <Toaster position="top-right" />
  </BrowserRouter>

  <Routes>
          <Route path="/" element={<MainContent />} />
          <Route path="/details/apn/:id" element={<DetailsAPN />} />
        </Routes>

import DetailsAPN from "./components/DetailsAPN";
import { Routes, Route } from "react-router-dom";
        
