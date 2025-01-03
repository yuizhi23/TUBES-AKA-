#ifndef HEADER_H_INCLUDED
#define HEADER_H_INCLUDED

#include <vector>
#include <utility>

using namespace std;

class Knapsack {
public:
    static int recursive(int kredit, const vector<pair<int, int>>& item, int n);
    static pair<int, vector<int>> iterative(int kredit, const vector<pair<int, int>>& item);
};


#endif // HEADER_H_INCLUDED

#include "header.h"
#include <vector>
#include <algorithm>

using namespace std;

int Knapsack::recursive(int kredit, const vector<pair<int, int>>& item, int n) {
    if (n == 0 || kredit == 0) {
        return 0;
    }

    if (item[n - 1].second > kredit) {
        return recursive(kredit, item, n - 1);
    }

    return max(
        recursive(kredit, item, n - 1),
        item[n - 1].first + recursive(kredit - item[n - 1].second, item, n - 1)
    );
}

pair<int, vector<int>> Knapsack::iterative(int kredit, const vector<pair<int, int>>& item) {
    int jumlahItem = item.size();
    vector<vector<int>> dp(jumlahItem + 1, vector<int>(kredit + 1, 0));
    vector<vector<vector<int>>> itemTerpilih(jumlahItem + 1, vector<vector<int>>(kredit + 1));

    for (int i = 1; i <= jumlahItem; i++) {
        for (int w = 0; w <= kredit; w++) {
            if (item[i - 1].second <= w) {
                int nilaiBaru = dp[i - 1][w - item[i - 1].second] + item[i - 1].first;
                if (nilaiBaru > dp[i - 1][w]) {
                    dp[i][w] = nilaiBaru;
                    itemTerpilih[i][w] = itemTerpilih[i - 1][w - item[i - 1].second];
                    itemTerpilih[i][w].push_back(i);
                } else {
                    dp[i][w] = dp[i - 1][w];
                    itemTerpilih[i][w] = itemTerpilih[i - 1][w];
                }
            } else {
                dp[i][w] = dp[i - 1][w];
                itemTerpilih[i][w] = itemTerpilih[i - 1][w];
            }
        }
    }

    return {dp[jumlahItem][kredit], itemTerpilih[jumlahItem][kredit]};
}

#include <iostream>
#include <vector>
#include "header.h"

using namespace std;

int main() {
    int kredit;
    int jumlahItem;

    cout << "Masukkan jumlah kredit yang tersedia: ";
    cin >> kredit;

    cout << "Masukkan jumlah item (senjata/peralatan): ";
    cin >> jumlahItem;

    vector<pair<int, int>> item(jumlahItem);

    for (int i = 0; i < jumlahItem; i++) {
        cout << "\nMasukkan nilai (efektivitas) untuk item " << i + 1 << ": ";
        cin >> item[i].first;
        cout << "Masukkan biaya (kredit) untuk item " << i + 1 << ": ";
        cin >> item[i].second;
    }

    pair<int, vector<int>> hasilIterative = Knapsack::iterative(kredit, item);
    cout << "\nNilai maksimum yang dapat dicapai: " << hasilIterative.first << endl;
    cout << "Item yang dibeli: ";
    for (int i : hasilIterative.second) {
        cout << "Item " << i << " ";
    }
    cout << endl;

    return 0;
}
