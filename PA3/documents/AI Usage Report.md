# Appendix A. AI Usage Notes

> **Course:** Introduction to Software Engineering
> **Project / Assignment:** `Dormify — Dormitory Management System`
> **Group / Student:** `Group 04`

---

## A.1. Summary Table

|  #  | Tool (name, version) | Purpose |
| :-: | -------------------- | ------- |
|  1  | `Gemini` | `Improving the UI for new sections and existing features` |
|  2  | `Claude + Claude Code` | `Fixing system bugs + strengthening the web application's security + implementing new features based on the original specification + combining Spec Kit with Claude Code to automate the coding workflow` |
|  3  | `ChatGPT + Gemini` | `Assisting with writing the PA3 documentation` |

---

## A.2. Usage Details by Team Member

### Đào Duy Anh (24127012) — `Used to automate the code generation workflow via Spec Kit`

| Item | Description |
| ---- | ----------- |
| **Tool / version** | `Claude Code + Spec Kit in the CLI` |
| **Access platform** | `Claude Code extension in Visual Studio Code, together with Spec Kit` |
| **Access time** | `Throughout the web application development process` |
| **Prompt used** | _"`Based on the spec files already configured for coding with Claude and Claude Code, please help me use the Spec Kit workflow in the chat's CLI to implement feature X so that it satisfies the requirements stated in the PA3 file I sent you`"_ |
| **Purpose of use** | `To automate the entire four-step spec workflow, then implement the code with Claude Code following the specification documents produced by the spec process. Finally, Claude Code was asked to run the test commands and verify the result directly in the dev environment once the tests passed` |
| **AI-generated content** | `The feature was completed 100%. The API endpoints and core logic were completed and ran correctly, with no bugs arising during real interaction. The interface was completed consistently with the rest of the application's UI` |
| **Student's edits / verification** | `Cross-checked against actual behaviour; re-ran the test cases` |
| **Evidence** | `https://www.youtube.com/watch?v=LdsMxwJSV5E` |

### Trần Huỳnh Mạnh Đạt (24127024) — `Used to write the PA3 documentation`

| Item | Description |
| ---- | ----------- |
| **Tool / version** | `ChatGPT + Gemini` |
| **Access platform** | `Web` |
| **Access time** | `Throughout the days of PA3` |
| **Prompt used** | _"`Please help me create a sample Use-Case Model and Use-Case Specifications based on the features specifically described in the file Những tính năng chính.docx. In addition, please find ways to improve the formatting of the existing documents such as the SDP and VD, and apply a similar format to the Use-Case Model and Use-Case Specifications.`"_ |
| **Purpose of use** | `To create an initial sample for the Use-Case Model and Use-Case Specifications based on the listed system features, and to make the format of these PA3 documents consistent with the existing SDP and VD documents` |
| **AI-generated content** | `AI suggested the structure, section layout, sample use-case model content, use-case specification templates, and formatting improvements such as headings, tables, numbering, and wording style aligned with the previously written SDP and VD documents` |
| **Student's edits / verification** | `Reviewed the generated samples against the feature list and PA3 requirements, corrected actor names and use-case flows, refined the wording, adjusted the formatting manually, and verified that the final documents were consistent with the project's actual scope` |

### Trần Hoàng Quốc Khánh (24127057) — `Used AI to help build the AI chatbot feature for the project`

| Item | Description |
| ---- | ----------- |
| **Tool / version** | `Gemini` |
| **Access platform** | `Web` |
| **Access time** | `Throughout PA3` |
| **Prompt used** | _"`I would like you to help me add an AI chatbot to the website using the Qwen 2.5:3b model on Hugging Face. For now the interface does not need much attention — please focus first on writing the logic for handling queries and answering based on basic documents.`"_ |
| **Purpose of use** | `To add an AI chatbot to the project in order to meet the project's AI integration requirement. AI was also used to improve the quality of the chatbot's answers.` |
| **AI-generated content** | `The AI successfully helped the group integrate the Qwen 2.5:3b model into the website, and the chatbot was able to answer basic questions such as greetings. The chatbot was additionally given the ability to read documents ingested into the database in order to explain content and rules related to the dormitory regulations` |
| **Student's edits / verification** | `Checked directly in the dev environment and used prompts to verify the model's answers` |

### Hồ Phúc Kiên (24127067) — `Used to improve the website's UI`

| Item | Description |
| ---- | ----------- |
| **Tool / version** | `Gemini + Claude` |
| **Access platform** | `Web` |
| **Access time** | `Throughout the days of PA3` |
| **Prompt used** | _"`The current interface has issue X. I want you to adjust it according to idea Z, and additionally add effects A, B, ...`"_ |
| **Purpose of use** | `To improve UI/UX elements that were faulty or not yet visually polished` |
| **AI-generated content** | `The interface was completed` |
| **Student's edits / verification** | `Verified directly in the dev environment and approved by all four team members` |

---

## A.3. Work Done Independently by the Students and Verification Methods

- **Done entirely by the students:** `algorithm design, implementation of module ..., running experiments and taking measurements, analysing results, writing the conclusion`
- **How AI-assisted content was edited:** `rewritten in the group's own wording, supplemented with examples from real data, notation unified with PA2`
- **Verification methods:** `cross-checked against course materials, re-ran the full test suite, compared against results in the dev environment`
- **Commitment:** all results, figures and images in the report were produced by programs the group actually ran; no simulated or fabricated data was used.
