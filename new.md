Let’s integrate a real-world Python library into your setup for the samplePyLibrary. We’ll use the popular NumPy library, which is widely used for numerical computing.

Step 1: Install NumPy in the Backend Environment

First, you need to install the NumPy library in the be folder where your WebSocket server will be running.

# Activate the virtual environment in the 'be' folder
source venv/bin/activate

# Install NumPy
pip install numpy

Step 2: Modify samplePyLibrary to Use NumPy

Now, let’s modify the samplePyLibrary to include functionality using NumPy. This will allow Monaco Editor to send some Python code that the backend can process using NumPy.

Update the samplePyLibrary/main.py to Use NumPy:

import numpy as np

def process_code(code):
    """
    This function will use NumPy to evaluate simple mathematical expressions.
    It will interpret the input `code` as a NumPy array operation.
    """
    try:
        # Evaluate the code using NumPy (for demo purposes, using eval with caution)
        result = eval(code, {"np": np})  # This allows using NumPy functions in the code input
        return f"Result: {result}"
    except Exception as e:
        return f"Error: {str(e)}"

Step 3: Modify the WebSocket Server to Use samplePyLibrary

Go to be/app.py and ensure it imports and uses the modified library:

import sys
sys.path.append('../samplePyLibrary')

from samplePyLib.main import process_code

async def handle_client(websocket, path):
    while True:
        try:
            # Receive the code from the frontend
            message = await websocket.recv()
            data = json.loads(message)
            code = data['code']

            # Process the code using the samplePyLibrary (which now uses NumPy)
            result = process_code(code)
            
            # Send the result back to the frontend
            await websocket.send(json.dumps({"result": result}))
        except websockets.exceptions.ConnectionClosed:
            print("Connection closed")
            break

async def main():
    async with websockets.serve(handle_client, "localhost", 8765):
        await asyncio.Future()  # Run forever

if __name__ == "__main__":
    asyncio.run(main())

Step 4: Run the WebSocket Server

Start the WebSocket server again to ensure it’s running with the newly integrated NumPy library:

python app.py

Step 5: Modify Angular Frontend (fe) to Test NumPy

You can now test NumPy from the Monaco Editor in the Angular frontend. Here’s an example of what you can do in the Monaco Editor:

In your Angular app, add code like this in the Monaco Editor to interact with the backend:

# Sample code to test NumPy
import numpy as np

arr = np.array([1, 2, 3, 4])
arr_sum = np.sum(arr)
arr_sum

When you type this into Monaco Editor, it will send the code to the backend WebSocket server, where it will be evaluated using NumPy.

Step 6: Test the Application

	1.	Start the Angular frontend (ng serve).
	2.	Write some code in Monaco Editor like the following:

import numpy as np
arr = np.array([1, 2, 3, 4])
np.sum(arr)


	3.	The WebSocket server will process the code using NumPy and return the result back to Monaco Editor (e.g., 10 for the sum of the array).

Summary

You now have a real-world Python library (NumPy) integrated with the WebSocket server and Monaco Editor. The editor sends Python code to the WebSocket server, which uses NumPy to evaluate the code and returns the result back to the frontend.

You can expand this setup to include more complex functionalities from libraries like Pandas, Scipy, or even machine learning libraries like Scikit-learn.