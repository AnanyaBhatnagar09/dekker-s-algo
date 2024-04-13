# dekker-s-algo
class DekkerMutex {
private:
    std::atomic<bool> flag[2];
    std::atomic<int> turn;

public:
    DekkerMutex() {
        flag[0] = flag[1] = false;
        turn = 0;
    }

    void lock(int id) {
        int other = 1 - id;
        flag[id] = true;
        while (flag[other]) {
            if (turn != id) {
                flag[id] = false;
                while (turn != id) {
                    // busy wait
                }
                flag[id] = true;
            }
        }
    }

    void unlock(int id) {
        turn = 1 - id;
        flag[id] = false;
    }
};

DekkerMutex mutex;
const int COUNT = 100000;
int counter = 0;

void increment(int id) {
    for (int i = 0; i < COUNT; ++i) {
        mutex.lock(id);
        ++counter;
        mutex.unlock(id);
    }
}
