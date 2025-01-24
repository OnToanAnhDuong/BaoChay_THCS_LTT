// 1. Tạo danh sách bài tập với trạng thái mặc định "chưa làm"
let exercises = Array.from({ length: 10 }, (_, i) => ({
    id: i + 1,
    done: false // Mặc định bài tập chưa được làm
}));
