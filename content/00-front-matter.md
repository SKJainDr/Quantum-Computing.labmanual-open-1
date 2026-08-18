<div class="box box-generic"><p>Q. C. Series | Laboratory Companion to Vol. I</p><p>Quantum Computing Specialization │ M.Sc. Physics Programme</p></div><div class="box box-generic"><p>QUANTUM COMPUTING</p><p>LABORATORY MANUAL — I</p><p>Foundational Quantum Experiments</p><p>Experiments 1–15 │ Guided Simulation + IBM Quantum Hardware</p><p>Includes: Full Theory · Simple &amp; Full Code Versions · Expected Output Diagrams</p><p>Circuit Visualisations · Observation Tables · Discussion Questions · Viva Q&amp;A (15–20 per Experiment)</p></div><table>
<colgroup>
<col style="width: 25%"/>
<col style="width: 25%"/>
<col style="width: 25%"/>
<col style="width: 25%"/>
</colgroup>
<thead>
<tr class="header">
<th><strong>AQLL v3.0 9 Modules</strong></th>
<th>IBM Quantum Cloud Hardware</th>
<th>Qiskit 1.x Python 3.10+</th>
<th>15 Experiments Foundational</th>
</tr>
</thead>
<tbody>
</tbody>
</table><p><strong>Dr. Sanjeev Kumar Jain</strong></p><p>Ex-Head, Department of Applied Sciences and Humanities</p><p>Faculty of Sciences, Invertis University, Bareilly</p><div class="box box-generic"><p>AQLL v3.0 + IBM Quantum Hardware | First Edition 2026</p></div><div class="box box-generic" style="text-align:center;"><p>Dedicated to</p><p>My Loving Brothers</p><p>Late Mr. Rajeev Kumar Jain</p><p>(My Elder Brother who inspires me to stand against challenges)</p><p>Mr. Anjeev Kumar Jain</p><p>Mr. Alok Kumar Jain</p></div><h1 id="preface">Preface</h1><p>This laboratory manual accompanies the course developed in Quantum Computing series, volume 1: Quantum Computers, forming an integral component of the M.Sc. Physics, Quantum Computing Specialization programme. It is designed as the foundational volume of a two-part laboratory series, providing students with their first systematic exposure to quantum computing through hands-on simulation and real cloud-based quantum hardware experiments.</p><p>This Quantum Computing Laboratory Manual I incorporates, for each of the fifteen experiments:</p><p>(1) an Expected Output section with output circuit diagrams and annotated console output tables that students can predict and verify,</p><p>(2) both a simple first program and a comprehensive full program version of the Qiskit code,</p><p>(3) complete observation tables for recording results,</p><p>(4) discussion questions and lab record requirements, and</p><p>(5) a Viva Voce Questions &amp; Answers section with 15–20 conceptual and practical questions per experiment.</p><p>These additions bridge the gap between running code and understanding the underlying physics and computer science, producing a document that is both a teaching resource and a practical laboratory guide.</p><p>The quantum computing revolution is no longer a distant prospect — it is an unfolding reality. IBM Quantum systems now offer cloud access to processors with hundreds of qubits, while simulation frameworks such as Qiskit enable students anywhere to explore quantum algorithms without requiring specialised laboratory hardware. This manual leverages both: the Advanced Quantum Learning Laboratory (AQLL) v3.0 simulation environment for guided, step-by-step exploration, and IBM Quantum cloud backends for authentic hardware execution.</p><h2 id="philosophy-and-pedagogy">Philosophy and Pedagogy</h2><p>This manual is structured around two complementary learning phases:</p><blockquote>
<p><strong>Phase 1 (Experiments 1–11)</strong> uses AQLL v3.0 — a comprehensive nine-module Qiskit simulation suite — to guide students through canonical quantum phenomena with rich, multi-panel visualisations. Every module is self-contained yet deliberately cross-referenced: the 4-qubit GHZ+i state explored in Experiment 1 reappears in density matrix analysis, entanglement quantification, noise modelling, and expectation value sweeps. This coherence ensures students encounter quantum phenomena not in isolation but as an interlocking framework.</p>
<p><strong>Phase 2 (Experiments 12–15)</strong> transitions to independent Qiskit programming. Students write full, commented programs from scratch — implementing quantum algorithms, running statistical tests, and submitting circuits to real IBM Quantum hardware. This phase develops computational autonomy essential for advanced quantum computing research.</p>
</blockquote><h2 id="how-to-use-this-manual">How to Use This Manual</h2><p>Each experiment follows a consistent structure:</p><ul>
<li><p>Background Theory introduces the relevant quantum concepts with equations in Dirac notation and clear physical explanations.</p></li>
<li><p>Gate Sequence or Protocol tables provide an operational step-by-step reference.</p></li>
<li><p>First Program gives a simple, short version of the Qiskit code that students can run immediately to verify their understanding.</p></li>
<li><p>Full Program provides the complete, richly commented Qiskit code with step-by-step gate analysis, multi-panel visualisations, and file outputs.</p></li>
<li><p>Expected Output sections describe expected visualisations, console outputs, and circuit diagrams.</p></li>
<li><p>Observation Tables provide structured recording sheets to be completed during the practical session.</p></li>
<li><p>Discussion Questions encourage deeper conceptual understanding.</p></li>
<li><p>Lab Record Requirements list mandatory tasks for the practical journal.</p></li>
<li><p>Viva Voce Q&amp;A provides 15–20 detailed questions and answers for exam preparation.</p></li>
</ul><p>Students are strongly encouraged to run the AQLL module first, study all visualisations carefully, and only then attempt the own-code version. The observation tables should be completed during the practical session, not retrospectively. The Viva Q&amp;A section should be studied after each experiment to consolidate understanding before the assessment.</p><h2 id="prerequisites">Prerequisites</h2><ul>
<li><p>Linear algebra: vectors, matrices, inner products, eigenvalues</p></li>
<li><p>Complex numbers and Euler's formula (e^{iθ} = cosθ + i sinθ)</p></li>
<li><p>Introductory quantum mechanics: Dirac notation, bra-ket formalism, measurement postulates</p></li>
<li><p>Basic Python programming: control flow, functions, NumPy arrays, Matplotlib</p></li>
<li><p>Understanding of probability and statistics at undergraduate level</p></li>
</ul><h2 id="safety-and-academic-integrity">Safety and Academic Integrity</h2><p>All IBM Quantum job IDs must be genuine — submitted from the student's own IBM Quantum account, retrievable at quantum.ibm.com/jobs at the time of viva assessment. Fabricated job IDs will result in loss of all IBM Hardware marks. Code submitted must be the student's own work; template code may be adapted but significant portions must be original.</p><p><strong>Dr. Sanjeev Kumar Jain</strong></p>