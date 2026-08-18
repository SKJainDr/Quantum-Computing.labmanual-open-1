<h1 id="experiment-2-density-matrix-analysis-and-von-neumann-entropy">Experiment 2 : Density Matrix Analysis and Von Neumann Entropy</h1><h2 id="background-theory-1">1. Background Theory</h2><p>The density matrix (also called the density operator) ρ provides the most general description of a quantum state, encompassing both pure states and statistical mixtures. For a pure state |ψ⟩, it is the outer product:</p><figure class="book-figure"><img loading="lazy" src="content/images/image9.png"/></figure><div class="box box-generic"><p> (outer product — pure state density matrix)</p></div><p>For a 4-qubit system the Hilbert space has dimension 2⁴ = 16, so ρ is a complex 16×16 matrix. The diagonal elements ρᵢᵢ = |⟨i|ψ⟩|² represent measurement probabilities in the computational basis. The off-diagonal elements ρᵢⱼ = ⟨i|ψ⟩⟨j|ψ⟩* are quantum coherences — they encode phase relationships and are destroyed by decoherence.</p><figure class="book-figure"><img loading="lazy" src="content/images/image9.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image10.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image11.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image12.png"/></figure><div class="box box-generic"><p>Key Formulae:</p><p> (outer product for pure state)</p><p>Purity:  → 1.0 for GHZ+i (pure state)</p><p>Von Neumann Entropy:  → 0.0 for pure state</p><p>Off-diagonal coherence: </p></div><table>
<colgroup>
<col style="width: 21%"/>
<col style="width: 25%"/>
<col style="width: 21%"/>
<col style="width: 32%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Quantity</strong></td>
<td><strong>Formula</strong></td>
<td><strong>Value for GHZ+i</strong></td>
<td><strong>Physical Meaning</strong></td>
</tr>
<tr class="even">
<td>Purity</td>
<td>γ = Tr(ρ²)</td>
<td>1.00000000</td>
<td>Pure state: γ=1; maximally mixed: γ=1/d</td>
</tr>
<tr class="odd">
<td>Von Neumann Entropy</td>
<td>S(ρ) = −Tr(ρ log₂ρ)</td>
<td>0.00000000</td>
<td>Pure state entropy = 0 (no classical uncertainty)</td>
</tr>
<tr class="even">
<td>Trace</td>
<td>Tr(ρ) = 1</td>
<td>1.00000000</td>
<td>Probability normalisation — always 1</td>
</tr>
<tr class="odd">
<td>Coherence |ρ₀,₁₅|</td>
<td>= |⟨0000|ψ⟩⟨ψ|1111⟩|</td>
<td>0.50000000</td>
<td>Off-diagonal coherence from entanglement</td>
</tr>
</tbody>
</table><h2 id="a.-reading-the-heatmaps">2a. Reading the Heatmaps</h2><p>The AQLL §2 output shows three heatmaps (Re(ρ), Im(ρ), and |ρ|). For the GHZ+i state, identify these four bright elements:</p><table>
<colgroup>
<col style="width: 23%"/>
<col style="width: 12%"/>
<col style="width: 17%"/>
<col style="width: 46%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Matrix Position</strong></td>
<td><strong>Re(ρ)</strong></td>
<td><strong>Im(ρ)</strong></td>
<td><strong>Physical Meaning</strong></td>
</tr>
<tr class="even">
<td>(0,0) — diagonal</td>
<td>+ 0.5</td>
<td>0.0</td>
<td>Probability weight of |0000⟩</td>
</tr>
<tr class="odd">
<td>(15,15) — diagonal</td>
<td>+ 0.5</td>
<td>0.0</td>
<td>Probability weight of |1111⟩</td>
</tr>
<tr class="even">
<td>(0,15) — off-diagonal</td>
<td>0.0</td>
<td>+ 0.5 (imaginary!)</td>
<td>Coherence |0000⟩↔︎|1111⟩ — the i-phase signature</td>
</tr>
<tr class="odd">
<td>(15,0) — off-diagonal</td>
<td>0.0</td>
<td>− 0.5 (imaginary!)</td>
<td>Complex conjugate coherence</td>
</tr>
<tr class="even">
<td>All others</td>
<td>0.0</td>
<td>0.0</td>
<td>No other basis states participate in GHZ+i</td>
</tr>
</tbody>
</table><h2 id="qiskit-code">2. Qiskit Code</h2><h3 id="first-program-simple-version-1">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># -------------------------------------------------------------------
# Experiment 2 — First Program: Density Matrix Analysis (simple version)
# Dr. S. K. Jain, India
# --------------------------------------------------------------------
from qiskit import QuantumCircuit
from qiskit.quantum_info import DensityMatrix, entropy, partial_trace
import numpy as np
qc = QuantumCircuit(4)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0,1); qc.cx(0,2); qc.cx(0,3)
dm = DensityMatrix(qc)
rho = dm.data
print(f'Purity Tr(rho^2) = {np.real(np.trace(rho@rho)):.10f}')
print(f'Von Neumann entropy = {float(entropy(dm,base=2)):.10f}')
print(f'Trace Tr(rho) = {np.real(np.trace(rho)):.10f}')
print('Non-zero elements:')
for i in range(16):
for j in range(16):
if abs(rho[i,j]) &gt; 0.01:
print(f' rho[{i:2d},{j:2d}] = {rho[i,j].real:+.6f} + {rho[i,j].imag:+.6f}i')</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image13.png"/></figure><h3 id="full-program-complete-version">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 2: Density Matrix Analysis and Von Neumann Entropy
# AQLL §2 | Dr. S. K. Jain, India
# ───────────────────────────────────────────────────────────────────────
from qiskit.quantum_info import DensityMatrix, Statevector, entropy, partial_trace
from qiskit import QuantumCircuit
import numpy as np, matplotlib.pyplot as plt
# Build GHZ+i state
qc = QuantumCircuit(4)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0,1); qc.cx(0,2); qc.cx(0,3)
# Full 16x16 density matrix
dm = DensityMatrix(qc)
rho = dm.data # 16x16 complex NumPy array
# Purity: Tr(ρ²)
purity = np.real(np.trace(rho @ rho))
print(f'Purity Tr(ρ²) = {purity:.10f} (expected: 1.0000000000)')
# Von Neumann entropy via eigenvalue decomposition
eigenvalues = np.linalg.eigvalsh(rho)
eigenvalues = eigenvalues[eigenvalues &gt; 1e-15] # Remove numerical zeros
S = -np.sum(eigenvalues * np.log2(eigenvalues))
print(f'Von Neumann entropy S(ρ) = {S:.10f} (expected: 0.0000000000)')
print(f'Trace Tr(ρ) = {np.real(np.trace(rho)):.10f} (expected: 1.0000000000)')
# Non-zero elements
print('\nNon-zero density matrix elements (|ρᵢⱼ| &gt; 0.01):')
print(f' {"Index":&gt;7} {"Re(ρ)":&gt;10} {"Im(ρ)":&gt;10} {"Abs":&gt;10}')
for i in range(16):
for j in range(16):
if abs(rho[i,j]) &gt; 0.01:
print(f' ρ[{i:2d},{j:2d}] = {rho[i,j].real:+.6f} + {rho[i,j].imag:+.6f}i |{abs(rho[i,j]):.6f}|')
# Three-panel heatmap visualisation
fig, axes = plt.subplots(1, 3, figsize=(17, 5))
im0 = axes[0].imshow(np.real(rho), cmap='RdBu_r', vmin=-0.5, vmax=0.5)
axes[0].set_title('Re(ρ) — Real Part', fontweight='bold', fontsize=12)
plt.colorbar(im0, ax=axes[0])
im1 = axes[1].imshow(np.imag(rho), cmap='RdBu_r', vmin=-0.5, vmax=0.5)
axes[1].set_title('Im(ρ) — Off-diagonal shows i-phase', fontweight='bold', fontsize=12)
plt.colorbar(im1, ax=axes[1])
im2 = axes[2].imshow(np.abs(rho), cmap='viridis', vmin=0, vmax=0.5)
axes[2].set_title('|ρᵢⱼ| — Coherence Map', fontweight='bold', fontsize=12)
plt.colorbar(im2, ax=axes[2])
for ax in axes:
ax.set_xlabel('Column index j'); ax.set_ylabel('Row index i')
ax.set_xticks([0,5,10,15]); ax.set_yticks([0,5,10,15])
plt.suptitle('GHZ+i State — 16×16 Density Matrix (Three Views)', fontsize=13, fontweight='bold')
plt.tight_layout()
plt.savefig('lab2_density_matrix.png', dpi=150, bbox_inches='tight')
plt.show()
# Partial density matrix for Q0 (trace out Q1,Q2,Q3)
rho_Q0 = partial_trace(dm, [1,2,3]) # Keep Q0; trace out Q1,Q2,Q3
purity_Q0 = np.real(np.trace(rho_Q0.data @ rho_Q0.data))
S_Q0 = float(entropy(rho_Q0, base=2))
print(f'\nReduced state ρ₀: Purity = {purity_Q0:.6f}, Entropy = {S_Q0:.6f} ebits')
print('(ρ₀ = I/2 if Q0 is maximally entangled with Q1Q2Q3)')</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image14.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image15.png"/></figure><pre><code>▶ Exp 2 — Expected Output &amp; Console Results
DENSITY MATRIX STRUCTURE (16×16):
Only 4 non-zero elements in the entire matrix:
col: 0 1 2 ... 14 15
row 0: [ 0.5 0 0 ... 0 +0.5i ]
row 1: [ 0 0 0 ... 0 0 ]
... [ ALL ZEROS ]
row15: [-0.5i 0 0 ... 0 0.5 ]
CONSOLE OUTPUT:
Purity Tr(ρ²) = 1.0000000000 (expected: 1.0000000000)
Von Neumann entropy S(ρ) = 0.0000000000 (expected: 0.0000000000)
Trace Tr(ρ) = 1.0000000000 (expected: 1.0000000000)
Non-zero density matrix elements:
ρ[ 0, 0] = +0.500000 + 0.000000i |0.500000|
ρ[ 0,15] = 0.000000 + 0.500000i |0.500000| ← i-phase signature!
ρ[15, 0] = 0.000000 - 0.500000i |0.500000|
ρ[15,15] = +0.500000 + 0.000000i |0.500000|
Reduced state ρ₀: Purity = 0.500000, Entropy = 1.000000 ebits
(ρ₀ = I/2 confirming Q0 is maximally entangled with Q1Q2Q3)</code></pre><h2 id="observation-and-results-1">3. Observation and Results</h2><h3 id="table-2.1-density-matrix-key-metrics">Table 2.1 — Density Matrix Key Metrics</h3><table style="width:100%;">
<colgroup>
<col style="width: 25%"/>
<col style="width: 21%"/>
<col style="width: 16%"/>
<col style="width: 16%"/>
<col style="width: 19%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Metric</strong></td>
<td><strong>Formula</strong></td>
<td><strong>AQLL Value</strong></td>
<td><strong>Own-Code Value</strong></td>
<td><strong>Expected Value</strong></td>
</tr>
<tr class="even">
<td>Purity Tr(ρ²)</td>
<td>Tr(ρ²)</td>
<td></td>
<td></td>
<td>1.00000000</td>
</tr>
<tr class="odd">
<td>Von Neumann Entropy</td>
<td>−Tr(ρ log₂ρ)</td>
<td></td>
<td></td>
<td>0.00000000</td>
</tr>
<tr class="even">
<td>Trace Tr(ρ)</td>
<td>Tr(ρ)</td>
<td></td>
<td></td>
<td>1.00000000</td>
</tr>
<tr class="odd">
<td>Number of eigenvalues &gt; 0</td>
<td>Count λᵢ &gt; 0</td>
<td></td>
<td></td>
<td>1</td>
</tr>
<tr class="even">
<td>|ρ₀,₁₅| (off-diagonal)</td>
<td>Coherence magnitude</td>
<td></td>
<td></td>
<td>0.50000000</td>
</tr>
<tr class="odd">
<td>Phase of ρ₀,₁₅</td>
<td>arg(ρ₀,₁₅) in radians</td>
<td></td>
<td></td>
<td>π/2 = 1.5708</td>
</tr>
</tbody>
</table><h3 id="table-2.2-non-zero-density-matrix-elements">Table 2.2 — Non-Zero Density Matrix Elements</h3><table>
<colgroup>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 14%"/>
<col style="width: 28%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Position (i,j)</strong></td>
<td><strong>Re(ρᵢⱼ) — Recorded</strong></td>
<td><strong>Im(ρᵢⱼ) — Recorded</strong></td>
<td><strong>|ρᵢⱼ|</strong></td>
<td><strong>Physical Interpretation</strong></td>
</tr>
<tr class="even">
<td>(0, 0)</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>(0, 15)</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>(15, 0)</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>(15, 15)</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h3 id="table-2.3-reduced-state-analysis-partial-trace">Table 2.3 — Reduced State Analysis (Partial Trace)</h3><table>
<colgroup>
<col style="width: 21%"/>
<col style="width: 17%"/>
<col style="width: 14%"/>
<col style="width: 18%"/>
<col style="width: 27%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Subsystem</strong></td>
<td><strong>Qubits Kept</strong></td>
<td><strong>Purity</strong></td>
<td><strong>Entropy (ebits)</strong></td>
<td><strong>State Type (Pure/Mixed)</strong></td>
</tr>
<tr class="even">
<td>Full system</td>
<td>Q0,Q1,Q2,Q3</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>Pair Q0,Q1</td>
<td>Q0,Q1</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>Pair Q2,Q3</td>
<td>Q2,Q3</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>Single Q0</td>
<td>Q0</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>Single Q3</td>
<td>Q3</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h2 id="discussion-questions-1">4. Discussion Questions</h2><ol type="1">
<li><p>Explain why the single-qubit reduced state ρ₀ = Tr_{Q1Q2Q3}(ρ) is a mixed state (purity &lt; 1) even though the overall system is in a pure state. How does this relate to entanglement?</p></li>
<li><p>What would the Im(ρ) heatmap look like for the standard GHZ state (phase θ=0) instead of GHZ+i? Sketch the expected pattern.</p></li>
<li><p>For the product state |+⟩⊗|+⟩⊗|+⟩⊗|+⟩, how many non-zero off-diagonal elements would the density matrix have? Calculate analytically.</p></li>
<li><p>The AQLL output reports S(ρ) = 0 for the full system but S(ρ₀) = 1 ebit per qubit. Explain this apparent paradox using the distinction between entanglement entropy and global entropy.</p></li>
<li><p>If we apply a depolarising noise channel to the GHZ+i state with parameter p=0.1, predict the new purity. Use the formula γ_noisy = (1−p)² γ_ideal + p²/d.</p></li>
</ol><h2 id="lab-record-requirements-1">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL §2. Screenshot all three heatmaps (Re, Im, |ρ|). Label the four bright spots.</p></li>
<li><p>Record all metrics in Table 2.1. Both AQLL and own-code columns must be filled.</p></li>
<li><p>Complete Tables 2.2 and 2.3.</p></li>
<li><p>Run the Full Program. Compute reduced matrices ρ₀₁ and ρ₀. Record purity for each.</p></li>
<li><p>Modify the GHZ state preparation to have phase θ=π instead of π/2. Re-run and describe how the Im(ρ) heatmap changes.</p></li>
<li><p>Write answers to all Discussion Questions with supporting calculations.</p></li>
</ul><table>
<colgroup>
<col style="width: 6%"/>
<col style="width: 93%"/>
</colgroup>
<tbody>
<tr class="odd">
<td colspan="2"><strong>⭐ VIVA VOCE QUESTIONS &amp; ANSWERS</strong></td>
</tr>
<tr class="even">
<td><strong>Q1</strong></td>
<td><strong>What is a density matrix and when do we need it instead of a state vector?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A density matrix ρ is a Hermitian, positive-semidefinite operator with Tr(ρ)=1 that represents the most general quantum state. We need it when: (1) the system is in a statistical mixture (classical uncertainty), (2) when describing a subsystem of an entangled system (reduced density matrix), or (3) for computing expectation values of observables via ⟨O⟩=Tr(ρO). A state vector |ψ⟩ is sufficient only for pure, isolated systems.</td>
</tr>
<tr class="even">
<td><strong>Q2</strong></td>
<td><strong>What is purity and what are its bounds?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Purity γ = Tr(ρ²) measures how 'pure' a quantum state is. Bounds: 1/d ≤ γ ≤ 1 where d is the Hilbert space dimension. γ=1 for a pure state (ρ=|ψ⟩⟨ψ|, a rank-1 projector). γ=1/d for the maximally mixed state ρ=I/d. For the 4-qubit GHZ+i state, d=16 and γ=1.0 exactly.</td>
</tr>
<tr class="even">
<td><strong>Q3</strong></td>
<td><strong>Explain Von Neumann entropy and its physical interpretation.</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>S(ρ) = −Tr(ρ log₂ρ) = −Σᵢ λᵢ log₂ λᵢ where {λᵢ} are eigenvalues of ρ. S=0 for a pure state (one eigenvalue =1, rest =0). S=log₂d for maximally mixed state. It quantifies the lack of information about the quantum state — equivalent to Shannon entropy of the eigenvalue distribution.</td>
</tr>
<tr class="even">
<td><strong>Q4</strong></td>
<td><strong>What are off-diagonal elements of a density matrix called, and what do they represent?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Off-diagonal elements ρᵢⱼ (i≠j) are called quantum coherences. They represent the phase relationship between basis states i and j in the quantum superposition. ρᵢⱼ = ⟨i|ψ⟩⟨j|ψ⟩* encodes the amplitude and phase of interference between states |i⟩ and |j⟩. When coherences are destroyed (decoherence), the density matrix becomes diagonal (classical mixture).</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>Why is Im(ρ₀,₁₅) = +0.5 for GHZ+i but 0 for standard GHZ?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For GHZ+i = (|0000⟩+i|1111⟩)/√2: ρ₀,₁₅ = ⟨0000|ψ⟩·(⟨ψ|1111⟩) = (1/√2)·(−i/√2)* = (1/√2)·(+i/√2) = +0.5i. For standard GHZ (no i), both amplitudes are real so Im(ρ₀,₁₅)=0.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>How do you compute the reduced density matrix for a subsystem?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The reduced density matrix ρ_A = Tr_B(ρ_AB) is obtained by partial trace over subsystem B. For qubit A in the GHZ+i state: ρ_A = Tr_{Q1Q2Q3}(ρ) = Σ_j ⟨j|_{Q1Q2Q3} ρ |j⟩_{Q1Q2Q3} = (1/2)|0⟩⟨0| + (1/2)|1⟩⟨1| = I/2. This is the maximally mixed single-qubit state, confirming Q0 is maximally entangled with Q1Q2Q3.</td>
</tr>
<tr class="even">
<td><strong>Q7</strong></td>
<td><strong>What does it mean for a density matrix to be Hermitian?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Hermitian means ρ† = ρ (the conjugate transpose equals the original). For density matrices, this guarantees all eigenvalues are real (they are probabilities), and the diagonal elements are real non-negative (measurement probabilities). The complex off-diagonal elements come in conjugate pairs: ρᵢⱼ = ρⱼᵢ*.</td>
</tr>
<tr class="even">
<td><strong>Q8</strong></td>
<td><strong>What happens to the density matrix under a depolarising noise channel?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Depolarising channel: ε(ρ) = (1−p)ρ + (p/d)I. This adds a scaled identity matrix, which fills the diagonal uniformly and reduces off-diagonal coherences by factor (1−p). Purity decreases: γ_new = (1−p)² + p²/d. In the heatmap, the bright spots at corners fade and a uniform background appears.</td>
</tr>
<tr class="even">
<td><strong>Q9</strong></td>
<td><strong>Prove that for any pure state, Tr(ρ²) = 1.</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For pure state ρ = |ψ⟩⟨ψ|: ρ² = |ψ⟩⟨ψ|ψ⟩⟨ψ| = |ψ⟩·1·⟨ψ| = |ψ⟩⟨ψ| = ρ. Therefore Tr(ρ²) = Tr(ρ) = 1. This also shows that for pure states, ρ is a projector (ρ²=ρ), unique to pure states.</td>
</tr>
<tr class="even">
<td><strong>Q10</strong></td>
<td><strong>What is quantum decoherence and how is it seen in the density matrix?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Decoherence is the loss of quantum coherence due to interaction with an environment, causing off-diagonal elements of ρ to decay toward zero. Lindblad equation: dρ/dt = −i[H,ρ] + Σ_k γ_k(L_kρL_k† − L_k†L_kρ/2 − ρL_k†L_k/2). In the heatmap, decoherence makes Im(ρ) spots at (0,15) and (15,0) fade — the i-phase signature disappears.</td>
</tr>
<tr class="even">
<td><strong>Q11</strong></td>
<td><strong>How many independent real parameters does a 4-qubit density matrix have?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A general d×d density matrix is Hermitian with unit trace, so it has d²−1 independent real parameters. For 4 qubits, d=2⁴=16, giving 16²−1=255 parameters. The GHZ+i state is a pure state (rank-1) — it has only 2d−2=30 independent parameters (2 real numbers per amplitude, minus global phase and normalisation).</td>
</tr>
<tr class="even">
<td><strong>Q12</strong></td>
<td><strong>What is the relationship between the density matrix and expectation values?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For any observable O, the expectation value is ⟨O⟩ = Tr(ρO). This is the most general formula, valid for both pure and mixed states. For a pure state |ψ⟩, this reduces to ⟨ψ|O|ψ⟩. For GHZ+i: ⟨ZZ⟩ = Tr(ρ·ZZ) = ρ₀₀·(+1) + ρ₁₅,₁₅·(+1) = 0.5+0.5 = +1.</td>
</tr>
<tr class="even">
<td><strong>Q13</strong></td>
<td><strong>Why is the density matrix more useful than the state vector for open quantum systems?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Open quantum systems interact with environments, causing mixtures and decoherence. A state vector can only describe pure states. The density matrix represents: (1) statistical mixtures of pure states, (2) the reduced state of a subsystem of a larger pure state, (3) the evolved state under Lindblad dynamics. All are essential for modelling real quantum hardware.</td>
</tr>
<tr class="even">
<td><strong>Q14</strong></td>
<td><strong>What is the significance of the eigenvalues of a density matrix?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The eigenvalues {λᵢ} of ρ are its 'principal probabilities' — probabilities of the system being found in each eigenstate. Constraints: λᵢ ≥ 0, Σᵢ λᵢ = 1. For GHZ+i (pure state), one eigenvalue =1 and all others =0. Von Neumann entropy = −Σ λᵢ log₂ λᵢ.</td>
</tr>
<tr class="even">
<td><strong>Q15</strong></td>
<td><strong>Describe the structure of the GHZ+i density matrix in the computational basis.</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>ρ_GHZ+i is a 16×16 matrix with only 4 non-zero elements: ρ₀₀=ρ₁₅,₁₅=+0.5 (real diagonal) and ρ₀,₁₅=+0.5i, ρ₁₅,₀=−0.5i (imaginary off-diagonal, representing the i-phase coherence). All 252 other elements are exactly zero.</td>
</tr>
</tbody>
</table><table>
<colgroup>
<col style="width: 12%"/>
<col style="width: 87%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>EXP</strong></p>
<p><strong>3</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Entanglement Quantification and Schmidt Decomposition</strong></p>
<p>AQLL §3 | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Measure single-qubit entanglement entropy, pairwise concurrence, and Schmidt coefficients for GHZ+i; identify the genuine multi-partite nature of GHZ entanglement.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §3 | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table>