# Lab 2: Install R and RStudio

This session introduces R and RStudio.

## Why learn R?

- You will need it for your final report (if you choose the report option).
- It supports your psychology coursework.
- It enhances your coding skills, which will help you in many domains of work, including utilising AI (!).

## Installing R

1. Visit CRAN at [https://cran.r-project.org/](https://cran.r-project.org/).
2. Select the version for your operating system (Windows, Mac, or Linux).
3. Download and install by following the on-screen instructions.

## Installing RStudio

See [Johannes Karl's video](#recommended-reading) for a walkthrough.

### Step 1: Install RStudio

1. Go to [https://www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/).
2. Choose the free version of RStudio Desktop and download it for your operating system.
3. Install RStudio Desktop.
4. Open RStudio to begin setting up your project environment.

### Step 2: Create a new project

1. In RStudio, go to `File > New Project`.
2. Choose `New Directory` for a new project or `Existing Directory` if you have a folder ready.
3. For a new project, select `New Project`, then provide a directory name.
4. Specify the location where the project folder will be created.
5. Click `Create Project`.

```admonish tip title="Order your workflow"
- Use a clear folder structure.
- If you are using GitHub, create a location on your machine (not Dropbox).
- If you are not using GitHub, choose the cloud (Dropbox or similar).
- When creating new files and scripts, use clear labels that anyone could understand. That "anyone" will be your future self.
```

### Step 3: Give the project structure

1. Within your project, create folders to organise scripts and data. Common folder names include `R/` for R scripts, `data/` for datasets, and `doc/` for documentation.
2. To create a new R script, go to `File > New File > R Script`. Save the script in your `R/` folder with a meaningful file name.
3. If you set up Git in week 1, you can initialise a Git repository when creating a new project to track your R work.

### Step 4: Working with R scripts

1. Write your R code in the script editor. Execute code by selecting lines and pressing `Ctrl + Enter` (Windows/Linux) or `Cmd + Enter` (Mac).
2. Use comments (preceded by `#`) to document your code.
3. Save your scripts regularly (`Ctrl + S` or `Cmd + S`).

### Step 5: When you exit RStudio

Before concluding your work, save your workspace or clear it to start fresh (`Session > Restart R`).

```admonish tip title="Workflow habits"
- Use clearly defined script names.
- Annotate your code.
- Save your scripts often (`Ctrl + S` or `Cmd + S`).
```

---

# Basic R Commands

```admonish note title="How to copy code from this page"
- Open `File > New File > R Script` in RStudio.
- Name and save your new R script.
- Copy the code blocks below into your script.
- Save: `Ctrl + S` or `Cmd + S`.
```

## Assignment (`<-`)

Assignment in R uses the `<-` operator:

```r
x <- 10 # assigns the value 10 to x
y <- 5  # assigns the value 5 to y
```

```admonish note title="RStudio shortcut for <-"
- **macOS:** `Option` + `-` (minus key)
- **Windows/Linux:** `Alt` + `-` (minus key)
```

## Concatenation (`c()`)

The `c()` function combines multiple elements into a vector:

```r
numbers <- c(1, 2, 3, 4, 5)
print(numbers)
```

## Operations (`+`, `-`)

```r
x <- 10
y <- 5

sum <- x + y
print(sum)

difference <- x - y
difference
```

```admonish warning title="Executing code"
`Ctrl + Enter` (Windows/Linux) or `Cmd + Enter` (Mac).
```

## Multiplication (`*`) and Division (`/`)

```r
product <- x * y
product

quotient <- x / y
quotient

# element-wise operations on vectors
vector1 <- c(1, 2, 3)
vector2 <- c(4, 5, 6)
vector_product <- vector1 * vector2
vector_product

vector_division <- vector1 / vector2
vector_division
```

Be mindful of division by zero: `10 / 0` returns `Inf`, and `0 / 0` returns `NaN`.

```r
# integer division and modulo
integer_division <- 10 %/% 3  # 3
remainder <- 10 %% 3          # 1
```

## `rm()` Remove Object

```r
devil_number <- 666
devil_number
rm(devil_number)
```

## Logic (`!`, `!=`, `==`)

```r
x_not_y <- x != y     # TRUE
x_equal_10 <- x == 10 # TRUE
```

## OR (`|` and `||`)

```r
# element-wise OR
vector_or <- c(TRUE, FALSE) | c(FALSE, TRUE) # c(TRUE, TRUE)

# single OR (first element only)
single_or <- TRUE || FALSE # TRUE
```

## AND (`&` and `&&`)

```r
# element-wise AND
vector_and <- c(TRUE, FALSE) & c(FALSE, TRUE) # c(FALSE, FALSE)

# single AND (first element only)
single_and <- TRUE && FALSE # FALSE
```

```admonish tip title="RStudio workflow shortcuts"
- **Execute code line:** `Cmd + Return` (Mac) or `Ctrl + Enter` (Win/Linux)
- **Insert section heading:** `Cmd + Shift + R` (Mac) or `Ctrl + Shift + R`
- **Align code:** `Cmd + Shift + A` (Mac) or `Ctrl + Shift + A`
- **Comment/uncomment:** `Cmd/Ctrl + Shift + C`
- **Save all:** `Cmd/Ctrl + Shift + S`
- **Find/replace:** `Cmd/Ctrl + F`
- **New file:** `Cmd/Ctrl + Shift + N`
- **Auto-complete:** `Tab`

For more, explore `Tools > Command Palette` or `Shift + Cmd/Ctrl + P`.
```

---

# Data Types in R

## Integers

```r
x <- 42L
str(x)         # int 42

y <- as.numeric(x)
str(y)         # num 42
```

Integers are useful for counts or indices that do not require fractional values.

## Characters

```r
name <- "Alice"
```

Characters represent text: names, labels, descriptions.

## Factors

```r
colors <- factor(c("red", "blue", "green"))
```

Factors represent categorical data with a limited set of levels.

### Ordered factors

```r
education_levels <- c("high school", "bachelor", "master", "ph.d.")

# unordered
education_factor_no_order <- factor(education_levels, ordered = FALSE)

# ordered
education_factor <- factor(education_levels, ordered = TRUE)
education_factor
```

Ordered factors support logical comparisons based on level order:

```r
edu1 <- ordered("bachelor", levels = education_levels)
edu2 <- ordered("master", levels = education_levels)
edu2 > edu1  # TRUE
```

---

# Strings

```r
you <- "world!"
greeting <- paste("hello,", you)
greeting  # "hello, world!"
```

# Vectors

Vectors are homogeneous: all elements must be of the same type.

```r
numeric_vector <- c(1, 2, 3, 4, 5)
character_vector <- c("apple", "banana", "cherry")
logical_vector <- c(TRUE, FALSE, TRUE, FALSE)
```

## Manipulating vectors

```r
vector_sum <- numeric_vector + 10
vector_multiplication <- numeric_vector * 2
vector_greater_than_three <- numeric_vector > 3
```

Accessing elements:

```r
first_element <- numeric_vector[1]
some_elements <- numeric_vector[c(2, 4)]
```

## Functions with vectors

```r
mean(numeric_vector)
sum(numeric_vector)
sort(numeric_vector)
unique(character_vector)
```

---

# Data Frames

## Creating data frames

```r
df <- data.frame(
  name = c("alice", "bob", "charlie"),
  age = c(25, 30, 35),
  gender = c("female", "male", "male")
)
head(df)
str(df)
```

## Accessing elements

```r
# by column name
names <- df$name

# by row and column
second_person <- df[2, ]
age_column <- df[, "age"]

# by condition
very_old_people <- subset(df, age > 25)
mean(very_old_people$age)
```

## Exploring data frames

```r
head(df)    # first six rows
tail(df)    # last six rows
str(df)     # structure
summary(df) # summary statistics
```

## Manipulating data frames

```r
# adding columns
df$employed <- c(TRUE, TRUE, FALSE)

# adding rows
new_person <- data.frame(name = "diana", age = 28, gender = "female", employed = TRUE)
df <- rbind(df, new_person)

# modifying values
df[4, "age"] <- 26

# removing columns
df$employed <- NULL

# removing rows
df <- df[-4, ]
```

## `rbind()` and `cbind()`

`rbind()` combines data frames by rows; `cbind()` combines by columns. When using these functions, column names (for `rbind`) or row counts (for `cbind`) must match. We will use `dplyr` for more flexible joining in later weeks.

---

# Summary statistics

```r
set.seed(12345)
vector <- rnorm(n = 40, mean = 0, sd = 1)
mean(vector)
sd(vector)
min(vector)
max(vector)
```

## `table()` for categorical data

```r
set.seed(12345)
gender <- sample(c("male", "female"), size = 100, replace = TRUE, prob = c(0.5, 0.5))
education_level <- sample(c("high school", "bachelor", "master"), size = 100, replace = TRUE, prob = c(0.4, 0.4, 0.2))
df_table <- data.frame(gender, education_level)
table(df_table)
table(df_table$gender, df_table$education_level)  # cross-tabulation
```

---

# First Data Visualisation with `ggplot2`

`ggplot2` is based on the Grammar of Graphics: you build plots layer by layer.

```r
library(ggplot2)

set.seed(12345)
student_data <- data.frame(
  name = c("alice", "bob", "charlie", "diana", "ethan", "fiona", "george", "hannah"),
  score = sample(80:100, 8, replace = TRUE),
  stringsAsFactors = FALSE
)
student_data$passed <- ifelse(student_data$score >= 90, "passed", "failed")
student_data$passed <- factor(student_data$passed, levels = c("failed", "passed"))
student_data$study_hours <- sample(5:15, 8, replace = TRUE)
```

## Bar plot

```r
ggplot(student_data, aes(x = name, y = score, fill = passed)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("failed" = "red", "passed" = "blue")) +
  labs(title = "student scores", x = "student name", y = "score") +
  theme_minimal()
```

## Scatter plot

```r
ggplot(student_data, aes(x = study_hours, y = score, color = passed)) +
  geom_point(size = 4) +
  scale_color_manual(values = c("failed" = "red", "passed" = "blue")) +
  labs(title = "scores vs. study hours", x = "study hours", y = "score") +
  theme_minimal()
```

## Box plot

```r
ggplot(student_data, aes(x = passed, y = score, fill = passed)) +
  geom_boxplot() +
  scale_fill_manual(values = c("failed" = "red", "passed" = "blue")) +
  labs(title = "score distribution by pass/fail status", x = "status", y = "score") +
  theme_minimal()
```

## Histogram

```r
ggplot(student_data, aes(x = score, fill = passed)) +
  geom_histogram(binwidth = 5, color = "black", alpha = 0.7) +
  scale_fill_manual(values = c("failed" = "red", "passed" = "blue")) +
  labs(title = "histogram of scores", x = "score", y = "count") +
  theme_minimal()
```

## Line plot (time series)

```r
months <- factor(month.abb[1:8], levels = month.abb[1:8])
study_hours <- c(0, 3, 15, 30, 35, 120, 18, 15)
study_data <- data.frame(month = months, study_hours = study_hours)

ggplot(study_data, aes(x = month, y = study_hours, group = 1)) +
  geom_line(linewidth = 1, color = "blue") +
  geom_point(color = "red", size = 1) +
  labs(title = "monthly study hours", x = "month", y = "study hours") +
  theme_minimal()
```

---

# Base R Graphs

Base R provides plotting functions without additional packages.

```r
# scatter plot
plot(student_data$study_hours, student_data$score,
     main = "scores vs. study hours", xlab = "study hours", ylab = "score",
     pch = 19, col = ifelse(student_data$passed == "passed", "blue", "red"))

# histogram
hist(student_data$score, breaks = 5, col = "skyblue",
     main = "histogram of student scores", xlab = "scores", border = "white")

# box plot
boxplot(score ~ passed, data = student_data,
        main = "score distribution by pass/fail status",
        xlab = "status", ylab = "scores", col = c("red", "blue"))
```

---

# Summary of Today's Lab

This session covered:

- Installing and setting up R and RStudio
- Basic arithmetic operations
- Data structures: vectors, factors, data frames
- Data visualisation with `ggplot2` and base R

## Where to get help

1. **Large language models:** LLMs are effective coding tutors. Help from LLMs for coding does not constitute a breach of academic integrity in this course. For your final report, cite all sources including LLMs.
2. [**Stack Overflow**](https://stackoverflow.com/questions/tagged/r): outstanding community resource.
3. [**Cross Validated**](https://stats.stackexchange.com/): best place for statistics advice.
4. **Developer websites:** [Tidyverse](https://github.com/tidyverse).
5. Your tutors and course coordinator.

## Recommended reading

- Wickham, H., & Grolemund, G. (2016). *R for Data Science.* O'Reilly Media. [Available online](https://r4ds.had.co.nz)
- Megan Hall's lecture: [https://meghan.rbind.io/talk/neair/](https://meghan.rbind.io/talk/neair/)
- RStudio learning materials: [https://education.rstudio.com/learn/beginner/](https://education.rstudio.com/learn/beginner/)
- Johannes Karl on getting started in R: [https://www.youtube.com/embed/haYxa3vWA28](https://www.youtube.com/embed/haYxa3vWA28)

---

# Appendix A: At-Home Exercises

```admonish note title="Exercise 1: Install the tidyverse package"
1. Open RStudio.
2. Go to `Tools > Install Packages...`.
3. Type `tidyverse` and check `Install dependencies`.
4. Click `Install`.
5. Load with `library(tidyverse)`.
```

```admonish note title="Exercise 2: Install the parameters and report packages"
1. Go to `Tools > Install Packages...`.
2. Type `parameters, report`.
3. Check `Install dependencies` and click `Install`.
```

```admonish note title="Exercise 3: Basic operations"
1. Create `vector_a <- c(2, 4, 6, 8)` and `vector_b <- c(1, 3, 5, 7)`.
2. Add them, subtract them, multiply `vector_a` by 2, divide `vector_b` by 2.
3. Calculate the mean and standard deviation of both.
```

```admonish note title="Exercise 4: Working with data frames"
1. Create a data frame with columns `id` (1–4), `name`, `score` (88, 92, 85, 95).
2. Add a `passed` column (pass mark = 90).
3. Extract name and score of students who passed.
4. Explore with `summary()`, `head()`, `str()`.
```

```admonish note title="Exercise 5: Logical operations and subsetting"
1. Subset `student_data` to find students who scored above the mean.
2. Create an `attendance` vector and add it as a column.
3. Subset to select only rows where students were present.
```

```admonish note title="Exercise 6: Cross-tabulation"
1. Create factor variables `fruit` and `colour`.
2. Make a data frame and use `table()` for cross-tabulation.
3. Which fruit has the most colour variety?
```

```admonish note title="Exercise 7: Visualisation with ggplot2"
1. Using `student_data`, create a bar plot of scores by name.
2. Add a title, axis labels, and colour by pass/fail status.
```

---

# Appendix B: Solutions

## Solution 3: Basic operations

```r
vector_a <- c(2, 4, 6, 8)
vector_b <- c(1, 3, 5, 7)

sum_vector <- vector_a + vector_b
diff_vector <- vector_a - vector_b
double_vector_a <- vector_a * 2
half_vector_b <- vector_b / 2

mean(vector_a); sd(vector_a)
mean(vector_b); sd(vector_b)
```

## Solution 4: Working with data frames

```r
student_data <- data.frame(
  id = 1:4,
  name = c("alice", "bob", "charlie", "diana"),
  score = c(88, 92, 85, 95),
  stringsAsFactors = FALSE
)
student_data$passed <- student_data$score >= 90
passed_students <- student_data[student_data$passed == TRUE, ]
summary(student_data)
```

## Solution 5: Logical operations and subsetting

```r
mean_score <- mean(student_data$score)
students_above_mean <- student_data[student_data$score > mean_score, ]

attendance <- c("present", "absent", "present", "present")
student_data$attendance <- attendance
present_students <- student_data[student_data$attendance == "present", ]
```

## Solution 6: Cross-tabulation

```r
fruit <- factor(c("apple", "banana", "apple", "orange", "banana"))
colour <- factor(c("red", "yellow", "green", "orange", "green"))
fruit_data <- data.frame(fruit, colour)
table(fruit_data$fruit, fruit_data$colour)
# apple has the most colour variety (red, green)
```

## Solution 7: Visualisation

```r
library(ggplot2)
ggplot(student_data, aes(x = name, y = score, fill = passed)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = c("TRUE" = "blue", "FALSE" = "red")) +
  labs(title = "student scores", x = "name", y = "score") +
  theme_minimal()
```

---

# Appendix C: Other Data Types

## Arrays and matrices

```r
matrix_1 <- matrix(1:9, nrow = 3)
array_1 <- array(1:12, dim = c(2, 3, 2))

# convert matrix to data frame
df_matrix_1 <- data.frame(matrix_1)
colnames(df_matrix_1) <- c("col_1", "col_2", "col_3")
```

## Lists

```r
my_list <- list(name = "John Doe", age = 30, scores = c(90, 80, 70))

# access elements
my_list$name
my_list[["scores"]]

# modify
my_list$gender <- "Male"
my_list$age <- 31
my_list$scores <- NULL

# lists as function return values
calculate_stats <- function(numbers) {
  list(mean = mean(numbers), sum = sum(numbers))
}
results <- calculate_stats(c(1, 2, 3, 4, 5))
```
