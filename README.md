## Features

- **Form Validation**: Uses `vee-validate` and `yup` for form validation.
- **Input Masking**: Implements input masking for the number field using `maska`.
- **Cancel Previous Requests**: Automatically cancels previous requests when a new one is made.
- **Error Handling**: Displays error messages if no users match the search criteria or other issues occur.

## Tech Stack

- **Frontend**: Vue.js with Composition API and TypeScript

## Installation

1. **Clone the repository:**

   ```bash
   git clone <repository-url>
   cd test-3205-frontend
   ```

2. **Install**

   ```bash
   cd test-3205-frontend
   npm install
   ```

3. **Run the frontend**

   ```bash
   npm run dev
   ```

## Usage

-- **Open the frontend application in your browser: http://localhost:5173**
-- **Fill in the email and number (optional) fields.**
-- **Click the "Submit" button to search for users.**
-- **If needed, click "Resend" to cancel the previous request and send a new one.**

## Example Request

-- **Enter an email, e.g., jim@gmail.com.**
-- **Optionally enter a number in the format 22-22-22.**
-- **Click "Submit" to see the search results below the form.**
-- **If the results are not as expected, click "Resend" to try again.**
