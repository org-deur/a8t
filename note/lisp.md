

Dưới đây là một số tài nguyên về **Common Lisp Runtime**, bao gồm cách hoạt động, quản lý bộ nhớ, và tối ưu hóa runtime:

---

### **1. Tổng quan về Common Lisp Runtime**
- **SBCL Internals**  
  [Steel Bank Common Lisp (SBCL) Internals](http://www.sbcl.org/internals/) - Một trong những tài liệu chi tiết nhất về cách SBCL triển khai runtime, quản lý bộ nhớ, và tối ưu hóa JIT.

- **CMUCL Internals**  
  [CMUCL Internals](https://www.cons.org/cmucl/) - CMUCL là tiền thân của SBCL, tài liệu này vẫn có giá trị để hiểu cách một Common Lisp runtime được thiết kế.

- **CLISP Internals**  
  [CLISP Implementation Notes](https://clisp.sourceforge.io/) - Mô tả cách CLISP triển khai một trình thông dịch Lisp với bytecode interpreter.

---

### **2. Quản lý Bộ Nhớ và Garbage Collection**
- **SBCL Garbage Collector**  
  [SBCL Garbage Collection](http://www.sbcl.org/manual/#Garbage-Collection) - Tài liệu chính thức về cách SBCL quản lý bộ nhớ với **generational garbage collection**.

- **MPS (Memory Pool System) cho Lisp**  
  [MPS Documentation](https://www.ravenbrook.com/project/mps/) - Một hệ thống quản lý bộ nhớ tùy chỉnh, được sử dụng để nghiên cứu tối ưu hóa bộ nhớ trong các Lisp runtime.

- **Understanding Lisp GC**  
  [The GC Handbook for Lisp](http://www.memorymanagement.org/) - Tổng quan về các kỹ thuật quản lý bộ nhớ trong Lisp, bao gồm compacting, generational, và conservative GC.

---

### **3. JIT Compilation và Performance**
- **SBCL Compiler and Runtime Optimization**  
  [SBCL Performance Notes](http://www.sbcl.org/manual/#Performance-Tuning) - Giải thích cách SBCL tối ưu hóa mã Lisp khi biên dịch.

- **CLISP Bytecode Interpreter**  
  [CLISP Bytecode Explanation](https://clisp.sourceforge.io/impnotes.html) - Mô tả cách CLISP sử dụng bytecode thay vì native compilation.

- **Common Lisp Performance Optimization**  
  - [Performance Optimization in Lisp](https://lispmethods.com/posts/performance/) - Hướng dẫn tối ưu hóa runtime của Lisp bằng profiling và tuning.

---

### **4. Implementing a Lisp Runtime**
- **"Lisp in Small Pieces" - Christian Queinnec**  
  - Một trong những cuốn sách quan trọng về cách xây dựng Lisp runtime, từ interpreter đến compiler.

- **"Anatomy of Lisp" - John Allen**  
  - Giải thích chi tiết về cách một Lisp runtime được thiết kế, bao gồm memory model, evaluation strategies.

- **"Build Your Own Lisp" - Daniel Holden**  
  [Online Book](http://www.buildyourownlisp.com/) - Hướng dẫn từng bước để xây dựng một Lisp runtime bằng C.

- **"Garbage Collection Handbook" - Richard Jones**  
  - Một cuốn sách toàn diện về các kỹ thuật GC, có nhiều phần liên quan đến Lisp.

---

### **5. Open Source Common Lisp Runtime Implementations**
- **SBCL** (Steel Bank Common Lisp): [GitHub](https://github.com/sbcl/sbcl)
- **CMUCL** (Carnegie Mellon University Common Lisp): [Website](https://www.cons.org/cmucl/)
- **CLISP** (GNU CLISP): [Website](https://clisp.sourceforge.io/)
- **ECL** (Embeddable Common Lisp): [GitHub](https://gitlab.com/embeddable-common-lisp/ecl)
- **Clozure CL**: [GitHub](https://github.com/Clozure/ccl)

---
