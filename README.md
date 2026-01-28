# ACRP-Lab
Jupyter notebooks to pre and post-process aircraft tire contact stresses and airfield flexible pavement data.

## Aircraft_CS.ipynb: Aircraft Tire Contact Stress Generator

This notebook computes the three-dimensional contact stress distributions beneath an aircraft tire and exports the results for further analysis (e.g., finite element modeling). The calculations are based on the methodology of Hernandez and Al-Qadi (2015), "Airfield Pavement Response Caused by Heavy Aircraft Takeoff".

### Main Features
- Calculates vertical (SSZ), longitudinal (SSX), and transverse (SSY) contact stresses for a multi-ribbed aircraft tire.
- Supports user-defined rib widths, load distribution, and stress factors.
- Exports results to Excel for use in pavement or structural analysis.
- Visualizes stress distributions with 3D plots.

### Key Equations and Methods

#### 1. **Vertical Contact Stress (SSZ)**
The vertical stress beneath each rib is calculated as:

$$
\text{SSZ}(x) = \frac{a \cdot P}{l \cdot b} \left(1 + \frac{1}{2n}\right) \left[1 - (x_{\text{norm}}^2)^n\right]
$$

Where:
- $a$ = load distribution factor for the rib
- $P$ = applied load [N]
- $l$ = contact length [mm]
- $b$ = contact width for the rib [mm]
- $n = \left| \frac{1}{2 \left( \frac{l b s \cdot \text{TiP}}{a P} - 1 \right)} \right|$
- $s$ = max vertical stress factor for the rib
- $\text{TiP}$ = tire inflation pressure [MPa]
- $x_{\text{norm}}$ = normalized position along the contact length, from $-1$ to $+1$:

$$
x_{\text{norm}} = \frac{(l_{\text{elem}} \cdot i) - \frac{l}{2}}{\frac{l}{2}}
$$

#### 2. **Longitudinal Contact Stress (SSX)**
The longitudinal stress is modeled as a piecewise rational quadratic (skewed parabola) to capture the asymmetric distribution along the contact length:

$$
\text{SSX}(x) = \text{TiP} \cdot s \cdot \frac{a_2 x^2 + a_1 x + a_0}{1 - a_3 x}
$$

The coefficients $a_0, a_1, a_2, a_3$ are determined so the curve passes through three control points (start, peak, and transition/end) for each segment (front and rear of the contact patch).

#### 3. **Transverse Contact Stress (SSY)**
The transverse stress is assumed proportional to the vertical stress:

$$
\text{SSY}(x) = \text{ssy\_factor} \times \text{SSZ}(x)
$$

Where $\text{ssy\_factor}$ is a user-defined constant (default 0.40).

### Assumptions
- The tire contact patch is divided into ribs, each with its own width and load share.
- The vertical stress distribution is symmetric about the center of the contact patch.
- The longitudinal stress distribution is asymmetric and modeled with a rational quadratic fit.
- The transverse stress is a fixed proportion of the vertical stress.
- The sum of the calculated vertical loads is checked against the applied load; a warning is issued if the difference exceeds a user-defined threshold (default 2.5%).

### References
- Hernandez, M. I., & Al-Qadi, I. L. (2015). Airfield Pavement Response Caused by Heavy Aircraft Takeoff. Transportation Research Record, 2474(1), 123-132.

### Usage
1. Set input parameters (load, geometry, rib properties) at the top of the notebook.
2. Run all cells to compute stresses and export results.
3. Check the equilibrium printout to ensure the calculated load matches the applied load within the threshold.
4. Use the generated Excel file and plots for further analysis.
