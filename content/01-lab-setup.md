<h1 id="setting-up-understanding-the">Setting-up &amp; Understanding the</h1><h1 id="quantum-computing-laboratory">Quantum Computing Laboratory</h1><h1 id="introduction-to-quantum-computing-laboratory-i">Introduction to Quantum Computing Laboratory I</h1><h2 id="the-quantum-computing-landscape">1.1 The Quantum Computing Landscape</h2><p>Classical computers represent information as bits — binary digits that are either 0 or 1. Quantum computers represent information as qubits, which can exist in superposition: a coherent combination of |0⟩ and |1⟩ simultaneously. This property, combined with entanglement and interference, enables quantum algorithms to achieve computational advantages that are impossible on classical hardware.</p><p>The past decade has witnessed remarkable progress: from single-qubit demonstrations to IBM’s 127-qubit Eagle, 433-qubit Osprey, and beyond. Cloud-based quantum computing — accessible to any researcher with an internet connection — has democratised access to real quantum hardware. This laboratory series exploits that access directly.</p><h2 id="aqll-v3.0-the-simulation-environment">1.2 AQLL v3.0 — The Simulation Environment</h2><p>The Advanced Quantum Learning Laboratory (AQLL) v3.0 is a comprehensive single-file Python simulation suite built on IBM Qiskit. It implements nine interconnected experimental modules covering the complete landscape of quantum information science:</p><table>
<colgroup>
<col style="width: 5%"/>
<col style="width: 19%"/>
<col style="width: 31%"/>
<col style="width: 43%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>§</strong></td>
<td><strong>Module</strong></td>
<td><strong>Title</strong></td>
<td><strong>Key Concepts</strong></td>
</tr>
<tr class="even">
<td>§1</td>
<td>GHZ State</td>
<td>GHZ+i State Evolution</td>
<td>Superposition, entanglement, phase, Bloch sphere</td>
</tr>
<tr class="odd">
<td>§2</td>
<td>Density Matrix</td>
<td>Density Matrix and Von Neumann Entropy</td>
<td>ρ=|ψ⟩⟨ψ|, purity, entropy, coherence</td>
</tr>
<tr class="even">
<td>§3</td>
<td>Entanglement</td>
<td>Entanglement Quantification</td>
<td>Entropy, concurrence, Schmidt decomposition</td>
</tr>
<tr class="odd">
<td>§4</td>
<td>Purity</td>
<td>Purity and Fidelity Tracker</td>
<td>Unitarity, gate-step purity conservation</td>
</tr>
<tr class="even">
<td>§5</td>
<td>Noise</td>
<td>Noise and Decoherence Laboratory</td>
<td>Depolarising, amplitude damping, Kraus operators</td>
</tr>
<tr class="odd">
<td>§6</td>
<td>Sampling</td>
<td>Sampling Laboratory</td>
<td>Shot noise, Born rule, quasi-probabilities</td>
</tr>
<tr class="even">
<td>§7a</td>
<td>Teleport</td>
<td>Quantum Teleportation Protocol</td>
<td>Bell pair, classical correction, fidelity=1</td>
</tr>
<tr class="odd">
<td>§7b</td>
<td>CHSH</td>
<td>CHSH Bell Inequality Violation</td>
<td>Non-locality, Tsirelson bound</td>
</tr>
<tr class="even">
<td>§7c</td>
<td>QFT</td>
<td>4-Qubit Quantum Fourier Transform</td>
<td>QFT circuit, phase staircase, Shor's link</td>
</tr>
<tr class="odd">
<td>§7d</td>
<td>Grover</td>
<td>Grover's Search Algorithm</td>
<td>Oracle, amplitude amplification</td>
</tr>
<tr class="even">
<td>§8</td>
<td>Sweeps</td>
<td>Expectation Value Sweeps</td>
<td>⟨ZZ⟩⟨XX⟩⟨YY⟩ correlations</td>
</tr>
</tbody>
</table><h2 id="system-requirements-and-setup">1.3 System Requirements and Setup</h2><table>
<colgroup>
<col style="width: 25%"/>
<col style="width: 32%"/>
<col style="width: 42%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Component</strong></td>
<td><strong>Requirement</strong></td>
<td><strong>Notes</strong></td>
</tr>
<tr class="even">
<td>Python</td>
<td>3.10 or later</td>
<td>3.11+ recommended for best compatibility</td>
</tr>
<tr class="odd">
<td>Qiskit</td>
<td>1.x (latest stable)</td>
<td>pip install qiskit</td>
</tr>
<tr class="even">
<td>Qiskit-Aer</td>
<td>0.14+</td>
<td>pip install qiskit-aer</td>
</tr>
<tr class="odd">
<td>NumPy</td>
<td>1.24+</td>
<td>pip install numpy</td>
</tr>
<tr class="even">
<td>Matplotlib</td>
<td>3.7+</td>
<td>pip install matplotlib</td>
</tr>
<tr class="odd">
<td>SciPy</td>
<td>1.10+</td>
<td>pip install scipy</td>
</tr>
<tr class="even">
<td>RAM</td>
<td>4 GB minimum</td>
<td>8 GB recommended for multi-module runs</td>
</tr>
<tr class="odd">
<td>Storage</td>
<td>~50 MB free</td>
<td>For output figures and report files</td>
</tr>
<tr class="even">
<td>IBM Quantum</td>
<td>Free account</td>
<td>Register at quantum.ibm.com</td>
</tr>
</tbody>
</table><h2 id="aqll-output-files">1.4 AQLL Output Files</h2><p>Each AQLL module generates standardised output files. Students must reference these files in their lab records:</p><table style="width:100%;">
<colgroup>
<col style="width: 38%"/>
<col style="width: 6%"/>
<col style="width: 46%"/>
<col style="width: 8%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>File Name</strong></td>
<td><strong>Type</strong></td>
<td><strong>Contents</strong></td>
<td><strong>Exp</strong></td>
</tr>
<tr class="even">
<td>S1_step0_initial.png … S1_step5_cnot3.png</td>
<td>PNG</td>
<td>GHZ+i gate-by-gate: Bloch + State City + histogram</td>
<td>1</td>
</tr>
<tr class="odd">
<td>circuit_GHZ.png</td>
<td>PNG</td>
<td>Full GHZ+i circuit diagram</td>
<td>1</td>
</tr>
<tr class="even">
<td>S2_density_matrix_heatmap.png</td>
<td>PNG</td>
<td>Re(ρ) and Im(ρ) heatmaps 16×16</td>
<td>2</td>
</tr>
<tr class="odd">
<td>S2_density_matrix_abs.png</td>
<td>PNG</td>
<td>Absolute coherence map |ρᵢⱼ|</td>
<td>2</td>
</tr>
<tr class="even">
<td>S3_entanglement.png</td>
<td>PNG</td>
<td>Single-qubit entropy and concurrence bar charts</td>
<td>3</td>
</tr>
<tr class="odd">
<td>S3_schmidt.png</td>
<td>PNG</td>
<td>Schmidt coefficients and entropy</td>
<td>3</td>
</tr>
<tr class="even">
<td>S4_purity_fidelity.png</td>
<td>PNG</td>
<td>Purity Tr(ρ²) and fidelity tracker</td>
<td>4</td>
</tr>
<tr class="odd">
<td>S5_noise.png</td>
<td>PNG</td>
<td>Fidelity and purity vs noise strength</td>
<td>5</td>
</tr>
<tr class="even">
<td>S6_sampling_shots.png</td>
<td>PNG</td>
<td>Shot-noise: 100/1000/10000 shots vs ideal</td>
<td>6</td>
</tr>
<tr class="odd">
<td>S7b_CHSH.png</td>
<td>PNG</td>
<td>CHSH parameter |S| vs angle; classical and Tsirelson bounds</td>
<td>8</td>
</tr>
<tr class="even">
<td>S7c_QFT.png</td>
<td>PNG</td>
<td>QFT output histogram and phase staircase</td>
<td>9</td>
</tr>
<tr class="odd">
<td>S7d_Grover.png</td>
<td>PNG</td>
<td>Grover amplitude oscillation vs iteration</td>
<td>10</td>
</tr>
<tr class="even">
<td>S8_expectation_sweep.png</td>
<td>PNG</td>
<td>⟨ZZ⟩⟨XX⟩⟨YY⟩ correlation sweeps vs parameter</td>
<td>11</td>
</tr>
<tr class="odd">
<td>metrics.csv</td>
<td>CSV</td>
<td>All 25+ numeric metrics from every section</td>
<td>All</td>
</tr>
<tr class="even">
<td>report.txt</td>
<td>TXT</td>
<td>Complete printed-console summary of all results</td>
<td>All</td>
</tr>
</tbody>
</table><h2 id="lab-record-submission-standards">1.5 Lab Record Submission Standards</h2><p>Every experiment must be recorded in a dedicated practical journal. The following structure is mandatory:</p><ul>
<li><p>Experiment number and title (matching this manual exactly)</p></li>
<li><p>Date performed, student name, roll number, and batch</p></li>
<li><p>Aim: one concise sentence stating what the experiment demonstrates</p></li>
<li><p>Theory: minimum 3 equations with Dirac notation, 4–6 sentences of explanation</p></li>
<li><p>AQLL screenshots: at least 2 figures from AQLL output with descriptive captions</p></li>
<li><p>Circuit diagram: generated using qc.draw('mpl') with all gate labels visible</p></li>
<li><p>Complete commented Qiskit code (First Program and Full Program, printed or pasted)</p></li>
<li><p>Observation tables: completed during the session, not retrospectively</p></li>
<li><p>Analysis: comparison table (simulation vs hardware where applicable), quantitative error discussion</p></li>
<li><p>Conclusions: 3–5 sentences summarising key findings</p></li>
<li><p>Answers to all Discussion Questions and Lab Record Requirements listed in this manual</p></li>
</ul><h2 id="ibm-quantum-hardware-access">1.6 IBM Quantum Hardware Access</h2><p>For experiments requiring IBM Quantum hardware (Experiments 13–15), students must:</p><ul>
<li><p>Create a free IBM Quantum account at quantum.ibm.com</p></li>
<li><p>Install the IBM Runtime package: pip install qiskit-ibm-runtime</p></li>
<li><p>Save credentials: QiskitRuntimeService.save_account(channel='ibm_quantum', token='YOUR_TOKEN')</p></li>
<li><p>Use service.least_busy() to select the optimal backend for each experiment</p></li>
<li><p>Record ALL job IDs immediately — these are required for assessment</p></li>
</ul><div class="box box-generic"><p>IMPORTANT: IBM Quantum job IDs are unique identifiers retrievable at quantum.ibm.com/jobs. Always screenshot the IBM Quantum dashboard immediately after job submission. Fabricated job IDs will result in zero marks for the hardware component.</p></div><p><img loading="lazy" src="content/images/image2.png"/></p>