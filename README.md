# Earth-Pollution-Simulator

Below is a detailed, step-by-step developer guide for building an **Earth Pollution Simulation** project using Python. This guide is intended for students with a basic knowledge of Python and focuses on building a simplified pollution diffusion simulation with visualization. The project is divided into four sessions (each roughly one hour) so that you can build upon previous work gradually.

---

## **Session 1: Environment Setup & Project Initialization**

### **1.1. Setting Up Your Python Environment**

- **Install Python:**  
  Ensure you have Python 3.8+ installed. Download it from [python.org](https://www.python.org/downloads/).

- **Create a Virtual Environment:**  
  In your terminal or command prompt, run:
  ```bash
  python -m venv env
  ```
  Then activate the environment:
  - **Windows:**
    ```bash
    env\Scripts\activate
    ```
  - **Mac/Linux:**
    ```bash
    source env/bin/activate
    ```

- **Install Dependencies:**  
  We will use NumPy for numerical operations and Matplotlib for visualization. Install them using pip:
  ```bash
  pip install numpy matplotlib
  ```
  *Explanation:*  
  NumPy enables fast array computations for our simulation grid, and Matplotlib provides tools to visualize the grid dynamics.

- **Reference:**  
  - [Python Virtual Environments Tutorial](https://realpython.com/python-virtual-environments-a-primer/)  
  - [NumPy Quickstart Tutorial](https://numpy.org/devdocs/user/quickstart.html) 

### **1.2. Initial Project Structure**

- **Create a Project Folder:**  
  Create a folder (e.g., `earth_pollution_sim`) and inside it, create a main script file (e.g., `simulation.py`).

- **Add Basic Comments & Structure:**  
  Open `simulation.py` and include a brief description of your simulation project.

---

## **Session 2: Building the Simulation Model**

### **2.1. Designing the Simulation Grid**

- **Objective:**  
  Create a 2D grid (using a NumPy array) to represent a simplified map. Pollution will be modeled as values in this grid.

- **Code Example:**  
  ```python
  import numpy as np
  import matplotlib.pyplot as plt

  # Define grid size and simulation parameters
  grid_size = (100, 100)  # 100x100 grid cells
  pollution_grid = np.zeros(grid_size)  # initialize grid with zero pollution

  # Add some initial pollution sources
  def add_pollution_source(grid, position, intensity=100):
      x, y = position
      grid[x, y] = intensity
      return grid

  # Example: Add pollution at three different locations
  pollution_grid = add_pollution_source(pollution_grid, (25, 25), 150)
  pollution_grid = add_pollution_source(pollution_grid, (50, 50), 200)
  pollution_grid = add_pollution_source(pollution_grid, (75, 75), 150)

  # Visualize the initial grid
  plt.imshow(pollution_grid, cmap='hot', interpolation='nearest')
  plt.title("Initial Pollution Distribution")
  plt.colorbar(label="Pollution Intensity")
  plt.show()
  ```
  *Explanation:*  
  This script initializes a grid of zeros, then adds pollution sources by assigning an intensity value to specific grid cells. The initial distribution is then visualized using Matplotlib's `imshow()`.

- **Reference:**  
  - [Matplotlib imshow Documentation](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.imshow.html) 

### **2.2. Simulating Pollution Diffusion**

- **Objective:**  
  Model the spread of pollution by simulating a diffusion process over the grid. We use a simple iterative method where each cell’s new value is an average of its own value and its neighbors'.

- **Code Example:**  
  ```python
  def diffuse(grid, diffusion_rate=0.1):
      # Create a copy to store updated values
      new_grid = grid.copy()
      # Loop through the grid (avoid edges for simplicity)
      for i in range(1, grid.shape[0] - 1):
          for j in range(1, grid.shape[1] - 1):
              # Average pollution from neighboring cells
              neighborhood = grid[i-1:i+2, j-1:j+2]
              new_grid[i, j] = grid[i, j] + diffusion_rate * (np.mean(neighborhood) - grid[i, j])
      return new_grid

  # Test one iteration of diffusion
  pollution_grid = diffuse(pollution_grid, diffusion_rate=0.2)

  plt.imshow(pollution_grid, cmap='hot', interpolation='nearest')
  plt.title("Pollution After Diffusion")
  plt.colorbar(label="Pollution Intensity")
  plt.show()
  ```
  *Explanation:*  
  The `diffuse()` function computes a new grid by updating each cell based on the difference between its current value and the average of its neighborhood. Adjust the `diffusion_rate` to control the speed of spread.

- **Reference:**  
  - [Introduction to Diffusion Simulation](https://www.youtube.com/watch?v=Gyk8IVN44bc) 

---

## **Session 3: Visualization with Animation**

### **3.1. Animating the Simulation with Matplotlib**

- **Objective:**  
  Use Matplotlib’s `FuncAnimation` to visualize the diffusion of pollution over time.

- **Code Example:**  
  ```python
  import matplotlib.animation as animation

  # Initialize the figure
  fig, ax = plt.subplots()
  im = ax.imshow(pollution_grid, cmap='hot', interpolation='nearest')
  ax.set_title("Earth Pollution Simulation")
  plt.colorbar(im, ax=ax, label="Pollution Intensity")

  def update(frame):
      global pollution_grid
      pollution_grid = diffuse(pollution_grid, diffusion_rate=0.2)
      im.set_data(pollution_grid)
      return [im]

  # Create the animation
  ani = animation.FuncAnimation(fig, update, frames=50, interval=200, blit=True)

  # To display the animation in a window
  plt.show()
  ```
  *Explanation:*  
  The `update()` function applies the diffusion process and updates the image data for each frame. `FuncAnimation` then creates an animation by repeatedly calling `update()`. Adjust the `frames` and `interval` parameters to control the duration and speed of the simulation.

- **Reference:**  
  - [Matplotlib Animation Tutorial](https://matplotlib.org/stable/api/animation_api.html) 

### **3.2. Optional: Saving the Animation**

- **Objective:**  
  Save your simulation as a video file.
  
- **Code Example:**  
  ```python
  ani.save("earth_pollution_simulation.mp4", writer='ffmpeg')
  ```
  *Explanation:*  
  Make sure you have [FFmpeg](https://ffmpeg.org/) installed on your system to save the animation as a video file.

- **Reference:**  
  - [Saving Animations in Matplotlib](https://matplotlib.org/stable/api/animation_api.html#matplotlib.animation.Animation.save) 

---

## **Session 4: Adding Interactivity and Enhancements**

### **4.1. Enhancing the Simulation**

- **Dynamic Pollution Sources:**  
  Add functionality to introduce new pollution sources during the simulation. For example, create a function that randomly adds pollution at intervals.
  ```python
  import random

  def add_random_source(grid, intensity_range=(100, 200)):
      x = random.randint(1, grid.shape[0] - 2)
      y = random.randint(1, grid.shape[1] - 2)
      intensity = random.randint(*intensity_range)
      grid[x, y] = intensity
      return grid
  ```

- **Parameter Adjustments:**  
  Allow users to adjust simulation parameters (e.g., diffusion rate, source intensity). This can be done by reading input from the console or using command-line arguments (e.g., with the `argparse` module).

- **Reference:**  
  - [Python argparse Tutorial](https://docs.python.org/3/library/argparse.html) 

### **4.2. Adding a GUI for Interactivity**

- **Option 1: Using Matplotlib Widgets**  
  Add slider widgets to adjust the diffusion rate or add a button to insert new pollution sources dynamically:
  ```python
  from matplotlib.widgets import Button, Slider

  # Define axes for widgets
  ax_button = plt.axes([0.8, 0.05, 0.1, 0.075])
  btn = Button(ax_button, 'Add Source')

  def on_click(event):
      global pollution_grid
      pollution_grid = add_random_source(pollution_grid)
  btn.on_clicked(on_click)

  # Define a slider for diffusion rate
  ax_slider = plt.axes([0.15, 0.05, 0.5, 0.03])
  slider = Slider(ax_slider, 'Diffusion Rate', 0.0, 1.0, valinit=0.2)

  def update_diffusion(val):
      global diffusion_rate
      diffusion_rate = slider.val
  slider.on_changed(update_diffusion)
  ```
  *Explanation:*  
  The button allows users to add a random pollution source. The slider adjusts the diffusion rate, which is then used in the `diffuse()` function.

- **Option 2: Building a Standalone GUI**  
  For a more advanced interface, consider using a Python GUI framework like Tkinter or PyQt. This is optional if your club prefers a web-based interface instead.

- **Reference:**  
  - [Matplotlib Widgets Overview](https://matplotlib.org/stable/gallery/widgets/index.html) 
  - [Tkinter Tutorial](https://realpython.com/python-gui-tkinter/) 

### **4.3. Final Testing and Presentation**

- **Test Your Simulation:**  
  Run the full simulation with interactive controls. Ensure that the pollution diffusion appears natural, and new sources are correctly integrated.
- **Polish the Visuals:**  
  Add titles, labels, and a legend if needed to explain the simulation to viewers.
- **Prepare for Exhibition:**  
  Document the simulation’s purpose, the model behind the diffusion, and instructions for interactive features.

---

By following these detailed steps, you will develop an interactive Earth Pollution Simulation in Python. This project not only teaches fundamental concepts such as numerical simulation and diffusion modeling but also offers practical experience with data visualization and basic interactivity—all of which contribute to an engaging Earth Day exhibition.

Happy coding, and enjoy exploring the dynamics of Earth’s pollution!
