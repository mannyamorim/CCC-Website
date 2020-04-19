---
title: "2020-S5 Josh\'s Double Bacon Deluxe - Brute Force"
---

# 2020-S5 Josh\'s Double Bacon Deluxe - Brute Force Solution

## Algorithm
This is the algorithm that we created in the third key insight section. This algorithm completes the first two subtasks sucessfully but runs out of time on the third.

## Read Input

{{< highlight cpp >}}
#define MAX_N 1000000
#define MAX_M 500001

int n;
int choices[MAX_N];
int last[MAX_M] = { -1 };

#define coach choices[0]
#define josh  choices[n - 1]

std::map<int, int> last_by_pos;

void read_input() {
	scanf("%d", &n);
	for (int i = 0; i < n; i++) {
		scanf("%d", &choices[i]);
		last[choices[i]] = i;
	}

	for (int i = 1; i < MAX_M; i++) {
		if (last[i] != -1 && i != coach && i != josh)
			last_by_pos.emplace(last[i], i);
	}
}
{{< /highlight >}}

## Solver

{{< highlight cpp >}}
double solve() {
	if (coach == josh)
		return 1.;

	double *solutions = new double[MAX_N];
	int *burgers = new int[MAX_M];
	memset(burgers, 0, MAX_M * sizeof(int));

	burgers[josh]++;
	burgers[coach]++;

	int i = n - 2;

	for (auto it = last_by_pos.crbegin(); it != last_by_pos.crend(); it++) {
		for (; i > it->first; i--)
			burgers[choices[i]]++;

		double prob = (double)burgers[coach] / (double)(n - i);

		for (auto sub_it = last_by_pos.crbegin(); sub_it != it; sub_it++) {
			prob += ((double)burgers[sub_it->second] / (double)(n - i)) * solutions[sub_it->first];
		}

		solutions[it->first] = prob;
	}

	for (; i > 0; i--)
		burgers[choices[i]]++;

	double prob = (double)burgers[coach] / (double)(n - i);

	for (auto it = last_by_pos.crbegin(); it != last_by_pos.crend(); it++) {
		prob += ((double)burgers[it->second] / (double)(n - i)) * solutions[it->first];
	}

	delete[] solutions;
	delete[] burgers;

	return prob;
}
{{< /highlight >}}

## Main

{{< highlight cpp >}}
int main() {
	read_input();
	double prob = solve();
	printf("%.10f", prob);
}
{{< /highlight >}}
