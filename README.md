# Metro-BUs-Route-planner
// DSA THEORY.cpp : Metro Bus Route Planner
#include <iostream>
#include <string>
using namespace std;

// Infinity value for distances
const int INF = 999;

// Forward declaration
class Stations;

// ================= PRIORITY QUEUE CLASS =================
class PriorityQueue
{
public:
    class Node
    {
    public:
        Stations* station;
        int distance;
        Node* next;

        Node(Stations* s, int d)
        {
            station = s;
            distance = d;
            next = nullptr;
        }
    };

    Node* front;

    PriorityQueue() 
    { 
        front = nullptr; 
    }

    void push(Stations* station, int distance)
    {
        Node* newnode = new Node(station, distance);

        if (!front || distance < front->distance)
        {
            newnode->next = front;
            front = newnode;
        }
        else
        {
            Node* temp = front;
            while (temp->next && temp->next->distance <= distance)
                temp = temp->next;

            newnode->next = temp->next;
            temp->next = newnode;
        }
    }

    Stations* pop()
    {
        if (!front)//if no front then queue is empty
            return nullptr;

        Node* temp = front;
        Stations* station = front->station;
        front = front->next;
        delete temp;
        return station;
    }

    bool is_empty() 
    { 
        return front == nullptr;
    }

    void update_priority(Stations* station, int newdistance)
    {
        Node* temp = front;
        Node* prev = nullptr;

        while (temp && temp->station != station)//temp!=null
        {
            prev = temp;//point to previous node used in deletion track 
            temp = temp->next;
        }

        if (temp && newdistance < temp->distance)//temp!=null mens node exist and if distance is less then existence node then remove exist node and push new smaller ditance 
        {
            if (prev)                    //a(2)->b(4)->c(8) if we delete b then a->c
                prev->next = temp->next;
            else
                front = temp->next;

            delete temp;
            push(station, newdistance);
        }
        else if (!temp)//temp==null
        {
            push(station, newdistance);
        }
    }
};

// STATION CLASS 
class Stations
{
public:
    string NAME;
    int ID;
    bool visited;
    int distance_from_source;
    Stations* next;
    Stations* pre;
    Stations* parent;

    Stations() : visited(false), distance_from_source(INF), next(nullptr), pre(nullptr), parent(nullptr) 
    {}
};

//  METRO STATION GRAPH 
class Metro_Station//like a linkled list
{
private:
    Stations* head;
    int count_stations;

    class Edge
    {
    public:
        Stations* destination;
        int weight;
        Edge* next;

        Edge(Stations* d, int w, Edge* n)
        {
            destination = d;
            weight = w;
            next = n;
        }
    };

    Edge** adjlist;//array of linkled list like station[0] ------ - `a->b->c->d and so on

public:
    Metro_Station()
    {
        head = nullptr;
        count_stations = 0;
        adjlist = nullptr;
    }

    ~Metro_Station()
    {
        // Delete adjacency lists
        for (int i = 0; i < count_stations; i++)//loop deletes all stations after deleting edges
        {
            Edge* e = adjlist[i];
            while (e)//delete edges
            {
                Edge* temp = e;
                e = e->next;
                delete temp;
            }
        }
        delete[] adjlist;

        // Delete stations
        if (head)//point to first station in circular list
        {
            Stations* temp = head->next;
            while (temp != head)
            {
                Stations* t = temp;
                temp = temp->next;
                delete t;
            }
            delete head;
        }
    }

    void insert_station(string name, int id)
    {
        Stations* newstation = new Stations;
        newstation->NAME = name;
        newstation->ID = id;

        if (!head)//head==null
        {
            newstation->next = newstation;
            newstation->pre = newstation;
            head = newstation;
        }
        else
        {
            Stations* temp = head->pre;
            temp->next = newstation;
            newstation->pre = temp;
            newstation->next = head;
            head->pre = newstation;
        }

        count_stations++;

        // Resize adjacency list
        Edge** newadj = new Edge * [count_stations];//add a new station we have to add new adjlist 
        for (int i = 0; i < count_stations - 1; i++)//copy old adjl and add new with this
            newadj[i] = adjlist[i];

        newadj[count_stations - 1] = nullptr;//newadjl has no connection yet sonull
        delete[] adjlist;
        adjlist = newadj;
    }

    Stations* find_id(int id)//to form a connection needed id 
    {
        if (!head) //head==null
            return nullptr;
        Stations* temp = head;
        do
        {
            if (temp->ID == id)
                return temp;
            temp = temp->next;
        } while (temp != head);

        return nullptr;//station with this id not found
    }

    int get_index(int id)//return the position of station becuase in adjlist station are placed according to index
    {
        if (!head)//no station found
            return -1;
        Stations* temp = head;
        int index = 0;
        do
        {
            if (temp->ID == id)
                return index;
            temp = temp->next;
            index++;
        } while (temp != head);

        return -1;
    }

    void add_connections(int from, int to, int distance)
    {
        Stations* A = find_id(from);
        Stations* B = find_id(to);

        if (!A || !B)
        {
            cout << "Station not found!" << endl;
            return;
        }

        int i = get_index(from);
        int j = get_index(to);

        adjlist[i] = new Edge(B, distance, adjlist[i]);
        adjlist[j] = new Edge(A, distance, adjlist[j]);
    }

    void reset_stations()
    {
        if (!head) 
            return;
        Stations* temp = head;
        do
        {
            temp->distance_from_source = INF;
            temp->visited = false;
            temp->parent = nullptr;//track previous node so that reconstruct the shortest path
            temp = temp->next;
        } while (temp != head);
    }

    void find_shortest_path(int src, int dest)
    {
        reset_stations();

        Stations* source = find_id(src);
        Stations* destination = find_id(dest);

        if (!source || !destination)
        {
            cout << "Invalid source or destination!" << endl;
            return;
        }

        source->distance_from_source = 0;
        PriorityQueue pq;
        pq.push(source, 0);

        while (!pq.is_empty())
        {
            Stations* u = pq.pop();
            if (!u)                        //cheak the station is null or not 
                break;
            u->visited = true;

            int idx = get_index(u->ID);      //position in adjlist
            Edge* edge = adjlist[idx];       //all the neighbours of u in list

            while (edge)                     //visited all neighbours and update the distance when shoorter then previous dista
            {
                Stations* v = edge->destination;//v=neighbour like scahool(3km)
                int newdist = u->distance_from_source + edge->weight;//0+3=3(v)

                if (newdist < v->distance_from_source)//3 less than infinity
                {
                    v->distance_from_source = newdist;//v=3
                    v->parent = u;
                    pq.update_priority(v, newdist);
                }
                edge = edge->next;
            }
        }

        // Print shortest distance and path
        if (destination->distance_from_source == INF)
        {
            cout << "No path found!" << endl;
            return;
        }

        cout << "Shortest distance: " << destination->distance_from_source << " km" << endl;
        cout << "Path: ";
        Stations* temp = destination;
        while (temp)
        {
            cout << temp->NAME;
            if (temp->parent) //arrows print when parent exits
                cout << " -> ";
            temp = temp->parent;//parent moves backward and print
        }
        cout << endl;
    }

    void display_stations_list()
    {
        if (!head)
            return;
        Stations* temp = head;
        cout << "Stations List:" << endl;
        do
        {
            cout << "ID: " << temp->ID << ", Name: " << temp->NAME << endl;
            temp = temp->next;
        } while (temp != head);
    }

    void display_connections()
    {
        if (!head) return;
        Stations* temp = head;
        int index = 0;
        cout << "Connections:" << endl;
        do
        {
            cout << temp->NAME << " -> ";
            Edge* e = adjlist[index];
            while (e)
            {
                cout << e->destination->NAME << "(" << e->weight << "km) ";
                e = e->next;
            }
            cout << endl;
            temp = temp->next;
            index++;
        } while (temp != head);
    }
};

// ================= MAIN FUNCTION =================
int main()
{
    Metro_Station metro;
    cout << "----------- Metro Bus Route Planner -----------" << endl;

    //  stations
    metro.insert_station("Home", 1);
    metro.insert_station("School", 2);
    metro.insert_station("University", 3);
    metro.insert_station("Hospital", 4);
    metro.insert_station("Market", 5);
    metro.insert_station("Airport", 6);

    //  connections
    metro.add_connections(1, 2, 3);
    metro.add_connections(2, 3, 5);
    metro.add_connections(1, 4, 4);
    metro.add_connections(4, 5, 2);
    metro.add_connections(3, 5, 6);
    metro.add_connections(5, 6, 10);
    metro.add_connections(3, 6, 15);

    int choice;
    do
    {
        cout << "\n========= MAIN MENU =========" << endl;
        cout << "1. Display all stations" << endl;
        cout << "2. Display all connections" << endl;
        cout << "3. Find shortest path" << endl;
        cout << "4. Add new station" << endl;
        cout << "5. Add new connection" << endl;
        cout << "6. Exit" << endl;
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice)
        {
        case 1:
            metro.display_stations_list();
            break;

        case 2:
            metro.display_connections();
            break;

        case 3:
        {
            int src, dest;
            cout << "Enter source station ID: ";
            cin >> src;
            cout << "Enter destination station ID: ";
            cin >> dest;
            metro.find_shortest_path(src, dest);
            break;
        }

        case 4:
        {
            string name;
            int id;
            cin.ignore();
            cout << "Enter station name: ";
            getline(cin, name);
            cout << "Enter station ID: ";
            cin >> id;
            metro.insert_station(name, id);
            cout << "Station added successfully!" << endl;
            break;
        }

        case 5:
        {
            int from, to, dist;
            cout << "Enter from station ID: ";
            cin >> from;
            cout << "Enter to station ID: ";
            cin >> to;
            cout << "Enter distance (km): ";
            cin >> dist;
            metro.add_connections(from, to, dist);
            cout << "Connection added successfully!" << endl;
            break;
        }

        case 6:
            cout << "Exiting program. Goodbye!" << endl;
            break;

        default:
            cout << "Invalid choice! Try again." << endl;
        }

    } while (choice != 6);

    return 0;
}
