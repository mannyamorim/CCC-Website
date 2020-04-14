---
title: "2020-S3 Searching for Strings - Solution"
---

# 2020-S3 Searching for Strings - Solution

## Read Input

{{< highlight cpp >}}
#define MAX_N 200000
#define MAX_H 200000
#define NUM_ALPHABET 26

int  n, h;
char needle  [MAX_N + 1];
char haystack[MAX_H + 1];
int  hist[NUM_ALPHABET] = { 0 };

void read_input() {
	fgets(needle, sizeof(needle), stdin);
	n = strlen(needle);
	if (needle[n - 1] == '\n') {
		needle[n - 1] == '\0';
		n--;
	}

	fgets(haystack, sizeof(haystack), stdin);
	h = strlen(haystack);
	if (haystack[h - 1] == '\n') {
		haystack[h - 1] == '\0';
		h--;
	}
}
{{< /highlight >}}

## Compute Histogram of Needle

{{< highlight cpp >}}
void compute_hist() {
	for (int i = 0; i < n; i++)
		hist[needle[i] - 'a']++;
}
{{< /highlight >}}

## Pre Hashed String View Class

{{< highlight cpp >}}
class pre_hashed_string_view {
public:
	pre_hashed_string_view(const char *str, int n, size_t hash) :
		str(str), n(n), hash(hash) {}
	bool operator ==(const pre_hashed_string_view &b) const {
		if (n != b.n)
			return false;
		return strncmp(str, b.str, n) == 0;
	}

	const char *str;
	int n;
	size_t hash;
};

namespace std {
	template<> struct hash<pre_hashed_string_view> {
		size_t operator()(pre_hashed_string_view const& obj) const noexcept {
			return obj.hash;
		}
	};
}
{{< /highlight >}}

## Compute Histograms of the Substrings in Haystacks

{{< highlight cpp >}}
/*
 * We compute a histogram of each of the possible substring positions
 * and compare with the original histogram of the needle string.
 * For each match we add the substring to a hashset and then return
 * the number of unique values.
 */
int solve() {
	int running_hist[NUM_ALPHABET] = { 0 };

	const int prime = 2000003;
	int power = 1;
	int hash = 0;

	for (int i = 0; i < n - 1; i++)
		power = (power * NUM_ALPHABET) % prime;

	/* compute histogram for the first position */
	for (int i = 0; i < n; i++) {
		running_hist[haystack[i] - 'a']++;
		hash = (NUM_ALPHABET * hash + (haystack[i] - 'a' + 1)) % prime;
	}

	std::unordered_set<pre_hashed_string_view> permutations;

	/* check the first position against the original histogram */
	if (memcmp(hist, running_hist, sizeof(int) * NUM_ALPHABET) == 0)
		permutations.emplace(haystack, n, hash);

	/* iterate through the remaining positions */
	for (int i = 0; i < h - n; i++) {
		/* update the histogram */
		running_hist[haystack[i]     - 'a']--;
		running_hist[haystack[i + n] - 'a']++;

		/* update the hash */
		hash = (NUM_ALPHABET * (hash - ((haystack[i] - 'a' + 1) * power)) + (haystack[i + n] - 'a' + 1)) % prime;
		if (hash < 0)
			hash = hash + prime;

		/* check the position */
		if (memcmp(hist, running_hist, sizeof(int) * NUM_ALPHABET) == 0)
			permutations.emplace(&haystack[i + 1], n, hash);
	}

	return permutations.size();
}
{{< /highlight >}}

## Main Function

{{< highlight cpp >}}
int main() {
	read_input();
	compute_hist();
	int permutations = solve();
	printf("%d", permutations);
	return 0;
}
{{< /highlight >}}
