#Metro bus route planner 
# 🚌 Metro Bus Route Planner (DSA Semester Project)

## 📘 Course Information

**Course:** Data Structures and Algorithms
**Semester:** III
**Instructor:** Dr. Sobia Khalid

---

## 👩‍💻 Project Team

* **Ayesha Afifa**
* **Ayesha Maqsood**
* **Areeba Habib**

---

## 📌 Project Overview

The **Metro Bus Route Planner** is a console-based C++ application that uses **graph-based data structures** and **Dijkstra’s Algorithm** to compute the shortest route between metro stations.

Metro stations are modeled as **nodes**, and the routes between them are represented as **weighted edges** (distance in kilometers). The system allows dynamic addition of stations and connections and provides an interactive, menu-driven interface for users.

---

## 🎯 Objectives

* Implement real-world routing using **graph algorithms**
* Apply **Dijkstra’s Algorithm** to find the shortest path
* Practice **dynamic memory management** in C++
* Use advanced data structures such as linked lists and priority queues
* Design an efficient and user-friendly menu-driven system

---

## 🧠 Data Structures Used

### 1️⃣ Graph Representation (Adjacency List)

* Implemented using **dynamic linked lists**
* Efficient for sparse networks
* Each edge stores:

  * Destination station
  * Distance (weight)
  * Pointer to next edge

### 2️⃣ Circular Doubly Linked List (Stations)

* Stores all metro stations
* Advantages:

  * No NULL pointer checks
  * Easy forward and backward traversal
  * Efficient insertion and deletion

### 3️⃣ Priority Queue (Min-Priority Queue)

* Custom implementation using linked list
* Used in Dijkstra’s Algorithm
* Supports:

  * Insert (push)
  * Remove minimum (pop)
  * Update priority (decrease key)

---

## 🚀 Algorithm Used

### Dijkstra’s Algorithm

* Used to find the **shortest path** between two stations
* Suitable because all distances are positive
* Maintains:

  * Distance from source
  * Parent pointers for path reconstruction

**Pseudocode:**

```
Initialize all distances = ∞
Set source distance = 0
Push source into priority queue
While queue is not empty:
    u = extract_min()
    For each neighbor v of u:
        If dist[v] > dist[u] + weight(u, v):
            dist[v] = dist[u] + weight(u, v)
            parent[v] = u
            update_priority(v)
```

---

## ⚙️ Features

* Display all metro stations
* Display all route connections
* Find shortest path between two stations
* Dynamically add new stations
* Dynamically add new connections
* Bidirectional routes (undirected graph)

---

## 🗂️ Project Structure

```
Metro-Bus-Route-Planner/
│── README.md
│── DSA THEORY.cpp
└── Output Screenshots (optional)
```

---

## ▶️ How to Run the Project

### Requirements

* C++ Compiler (GCC recommended)

### Compile and Run

```bash
g++ "DSA THEORY.cpp" -o metro
./metro
```

---

## 📊 Sample Output

* List of all metro stations
* List of all connections with distances
* Shortest path displayed with total distance
* Example Path:

```
Home -> School -> University -> Market
Shortest Distance: 14 km
```

---

## ✅ Strengths of the System

* Fully functional Dijkstra’s Algorithm
* Efficient memory usage
* Dynamic graph operations
* Clear path visualization
* Interactive and user-friendly menu

---

## 🔮 Future Enhancements

* Graphical User Interface (GUI)
* Real-time traffic integration
* Fare calculation
* File-based or database storage
* GPS-based route mapping

---

## 📜 License

This project is developed for **academic purposes** and is free to use for learning and reference.

---

⭐ *If you find this project helpful, consider giving it a star on GitHub!*

