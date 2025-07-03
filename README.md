# 3D-Surface-Map-of-Cameroon-s-Mineral-Resource-Sites-with-Flowing-Labels-over-Terrain-code
3D Surface Map of Cameroon’s Mineral Resource Sites with Flowing Labels over Terrain code

import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
import numpy as np
from scipy.interpolate import griddata

# Resource data
resource_data = {
    "Resource": [
        "Bauxite", "Iron Ore", "Gold", "Diamond", "Cobalt-Nickel-Manganese",
        "Limestone", "Uranium", "Tin & Tungsten", "Rutile & Ilmenite",
        "Oil & Gas", "Quartz & Feldspar"
    ],
    "Location": [
        "Minim-Martap", "Mbalam", "Batouri", "Mobilong", "Nkamouna",
        "Figuil", "Poli", "Mayo-Darlé", "Dehane", "Limbe", "Bafoussam"
    ],
    "Latitude": [6.43, 2.2167, 4.4333, 3.52, 3.40, 9.767, 8.4333, 6.433, 3.15, 4.01347, 5.45],
    "Longitude": [12.54, 13.8167, 14.3667, 15.05, 13.57, 13.967, 13.25, 11.517, 10.00, 9.12732, 10.42]
}

# Define the surface
x = np.linspace(min(resource_data['Longitude']) - 1, max(resource_data['Longitude']) + 1, 100)
y = np.linspace(min(resource_data['Latitude']) - 1, max(resource_data['Latitude']) + 1, 100)
x_grid, y_grid = np.meshgrid(x, y)
z_grid = 0.1 * np.sin((x_grid - 12)) + 0.1 * np.cos((y_grid - 6))  # smooth variation

# Interpolate Z for each (lon, lat) location
surface_points = np.column_stack((x_grid.ravel(), y_grid.ravel()))
surface_heights = z_grid.ravel()
locations = np.column_stack((resource_data['Longitude'], resource_data['Latitude']))
z_labels = griddata(surface_points, surface_heights, locations, method='cubic')

# Create 3D plot
fig = plt.figure(figsize=(17, 12))
ax = fig.add_subplot(111, projection='3d')

# Plot surface
ax.plot_surface(x_grid, y_grid, z_grid, cmap='terrain', alpha=0.5)

# Scatter resource points
ax.scatter(
    resource_data['Longitude'],
    resource_data['Latitude'],
    z_labels + 0.05,
    c='red', s=60, label='Resource Sites'
)

# Annotate each resource with flowing text over the surface
for i in range(len(resource_data['Resource'])):
    label = f"{resource_data['Resource'][i]}\n({resource_data['Location'][i]})"
    ax.text(
        resource_data['Longitude'][i],
        resource_data['Latitude'][i],
        z_labels[i] + 0.12,  # float above surface
        label,
        fontsize=7, color='black', ha='center'
    )

# Label axes and view
ax.set_title("Surface Map with Resource Labels Flowing Over Terrain", fontsize=12)
ax.set_xlabel("Longitude")
ax.set_ylabel("Latitude")
ax.set_zlabel("Surface Elevation")

ax.view_init(elev=25, azim=135)
ax.legend(loc='best')

plt.tight_layout()
plt.show()



