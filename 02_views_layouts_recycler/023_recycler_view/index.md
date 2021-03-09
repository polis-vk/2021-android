---
layout: default
title: RecyclerView
highlight: true
---

В этом уроке мы научимся создавать и отображать списки элементов при помощи `RecyclerView`.

## Обзор

`RecyclerView` - это `View` которое может отображать множество элементов в виде списка. Может прокручиваться для просмотра элементов не вошедших на один экран. Ячейки списка ушедшие за пределы экрана переиспользуются для отображения следующих элементов.

<img src="img/recycling_of_views.png" width="1200px"/>


## Использование RecyclerView

Для того, что бы начать использовать `RecyclerView`, необходимо добавить зависимость в `build.gradle` файл:

```groovy
dependencies {
...
    compile 'com.android.support:recyclerview-v7:28.0.0'
...
}
```

Добавим компонент `RecyclerView` в наш макет:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/activity_main__rv_movies"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</FrameLayout>
```

Запустив проект с такой версткой мы увидим пустой белый экран.
Попробуем отобразить какие-то данные в нашем списке. 

Предположим, что мы хотим сделаем приложение-список фильмов. Для начала создадим класс `Movie`, который будет содержать краткую информацию о конкретном фильме.

```java
public class Movie {

    public final String name;
    public final String description;
    public final int poster;

    public Movie(String name, String description, int poster) {
        this.name = name;
        this.description = description;
        this.poster = poster;
    }

}
```

Сущность фильма имеет: имя, описание, изображение. Поле `poster` имеет тип `int` потому что картинка будет взята из ресурсов приложения.

Как будет выглядеть ячейка с нашим фильмом? Давайте сверстаем новый макет: нам необходимо расположить два `TextView` и один `ImageView`.

```xml
<<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/movie_item__iv_poster"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_margin="10dp"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:src="@drawable/cat" />

    <TextView
        android:id="@+id/movie_item__tv_name"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:ellipsize="end"
        android:fontFamily="sans-serif-medium"
        android:maxLines="2"
        android:paddingStart="10dp"
        android:paddingLeft="10dp"
        android:paddingEnd="10dp"
        android:paddingRight="10dp"
        android:textSize="20sp"
        app:layout_constraintBottom_toTopOf="@+id/movie_item__tv_description"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@+id/movie_item__iv_poster"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_chainStyle="packed"
        tools:text="Movie name" />

    <TextView
        android:id="@+id/movie_item__tv_description"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:ellipsize="end"
        android:maxLines="4"
        android:paddingStart="10dp"
        android:paddingLeft="10dp"
        android:paddingEnd="10dp"
        android:paddingRight="10dp"
        android:textSize="10sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@+id/movie_item__iv_poster"
        app:layout_constraintTop_toBottomOf="@+id/movie_item__tv_name"
        tools:text="Movie description" />

</android.support.constraint.ConstraintLayout>
```

Для того что бы сообщить RecyclerView какие данные следует отображать, используется Adapter. Адаптер соединяет данные с представлением. Адаптер, который используется в RecyclerView, должен наследоваться от абстрактного класса RecyclerView.Adapter. Этот класс определяет три метода:

- `onCreateViewHolder`: возвращает объект `ViewHolder`, который будет хранить данные по одному объекту Movie;
- `onBindViewHolder`: выполняет привязку объекта `ViewHolder` к объекту Phone по определенной позиции;
- `getItemCount`: возвращает количество объектов в списке.

Выше мы упомянули `ViewHolder`. `ViewHolder` - это класс, объекты которого, хранят необходимую информацию для отрисовки ячеек списка.

Попробуем написать адаптер для списка фильмов:

```java
public class MovieAdapter extends RecyclerView.Adapter<MovieAdapter.MovieViewHolder> {

    private final List<Movie> movies;

    public MovieAdapter(List<Movie> movies) {
        this.movies = movies;
    }

    @NonNull
    @Override
    public MovieViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int i) {
        View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.movie_item, viewGroup, false);
        return new MovieViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull MovieViewHolder viewHolder, int i) {
        viewHolder.bind(movies.get(i));
    }

    @Override
    public int getItemCount() {
        return movies.size();
    }

    static final class MovieViewHolder extends RecyclerView.ViewHolder {

        private final TextView nameTextView;
        private final TextView descriptionTextView;
        private final ImageView posterImageView;

        public MovieViewHolder(@NonNull View itemView) {
            super(itemView);
            nameTextView = itemView.findViewById(R.id.movie_item__tv_name);
            descriptionTextView = itemView.findViewById(R.id.movie_item__tv_description);
            posterImageView = itemView.findViewById(R.id.movie_item__iv_poster);
        }

        private void bind(@NonNull Movie movie) {
            nameTextView.setText(movie.name);
            descriptionTextView.setText(movie.description);
            posterImageView.setImageResource(movie.poster);
        }

    }

}
```

Теперь свяжем адаптер со списком:
```java
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        RecyclerView recyclerView = findViewById(R.id.activity_main__rv_movies);
        MovieAdapter movieAdapter = new MovieAdapter(generateMovieList());
        recyclerView.setAdapter(movieAdapter);
    }

```

Запустив приложение, мы снова увидим белый экран. Почему? Нужно подсказать `RecyclerView` как располагать элементы. Для этого вызовем метод `setLayoutManager` и выставим `LinearLayoutManager`.

```java
LinearLayoutManager mLayoutManager = new LinearLayoutManager(this);
recyclerView.setLayoutManager(mLayoutManager);
```

<img src="img/movies.png" width="400px"/>

`LinearLayoutManager` это самой простой способ организации ячеек внутри списка. Вы можете менять менеджер по своему усмотрения, в зависимости от желаемого конечно вида вашего списка. Например, поменяв менеджер на `GridLayoutManager` можно добиться следующего отображения:

<img src="img/grid_manager.png" width="400px"/>

Теперь давайте разберемся как добавлять элементы в список. Для этого создадим `FabButton`. Floating action button это круглая кнопка предназначенная для выполнения самого главного действия на этом экране.

```xml
   <android.support.design.widget.FloatingActionButton
        android:id="@+id/activity_main__fb_add"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="20dp"
        android:tint="@android:color/white"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:srcCompat="@android:drawable/ic_input_add" />
```

## Действия с RecyclerView

Что бы сделать какое-то действие по нажатию на кнопку, нужно добавить `OnClickListener`. Это интерфейс, который имеет единственный метод `onClick`, этот метод будет вызвал если пользователь нажимает на кнопку. Сделаем так, что бы по клику в наш список добавлялся новый фильм:

```java
    private void setupFabButton() {
        FloatingActionButton fab = findViewById(R.id.activity_main__fb_add);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                movies.add(generateNewMovie());
                movieAdapter.notifyDataSetChanged();
            }
        });
    }
```

<img src="img/add_new_movie.png" width="400px"/>

А теперь давайте сделаем так, что бы наши ячейки были тоже кликабельными. Для этого добавим лисенер для строк списка. Пускай по клику на строку появится короткое сообщение с названием выбранного фильма:

```java

    private final Listener onMovieClickListener;

    public MovieAdapter(List<Movie> movies, Listener onMovieClickListener) {
        this.movies = movies;
        this.onMovieClickListener = onMovieClickListener;
    }

    @NonNull
    @Override
    public MovieViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int i) {
        View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.movie_item, viewGroup, false);
        view.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                onMovieClickListener.onMovieClick((Movie) v.getTag());
            }
        });
        return new MovieViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull MovieViewHolder viewHolder, final int i) {
        Movie movie = movies.get(i);
        viewHolder.bind(movie);
        viewHolder.itemView.setTag(movie);
    }

    interface Listener {

        void onMovieClick(Movie movie);

    }

}
```

```java
 movieAdapter = new MovieAdapter(movies, new MovieAdapter.Listener() {
            @Override
            public void onMovieClick(Movie movie) {
                Toast.makeText(MainActivity.this, movie.name, Toast.LENGTH_SHORT).show();
            }
        });
```

<img src="img/toast.png" width="400px"/>

Простейший пример использования `RecyclerView` можно посмотреть [тут](https://github.com/otopba/polis-mail-ru-recyclerview-sample)

## Что почитать

- https://developer.android.com/guide/topics/ui/layout/recyclerview
- https://google-developer-training.gitbooks.io/android-developer-fundamentals-course-concepts/content/en/Unit%202/44_c_recyclerview.html